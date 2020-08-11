# README
ARGoS3-CMAES-XnES
=====================


## Description

The code maintained on this repo allows to use xNES and CMA-ES to optimise a neural network control software for the [ARGoS3 simulator](https://github.com/ilpincy/argos3).
The optimization algorithm are adaptations of the [pagmo2 library](https://github.com/esa/pagmo2). Multi-threading using [open MPI](https://github.com/open-mpi/ompi) has been implemented for CMA-ES and xNES for cluster use.


## Package content

- `bin` This empty folder will contain the executable files.
- `params` This directory contains parameters used for the CMA-ES and xNES based design methods
- `src` This directory contains the source code for the design methods implemented in this repository
    - `genome_parser` visualizer for single layer Neural Network control software.
    - `NEAT` This directory contains the library used for the neural network topology.
    - `pagmo` The modified pagmo2 library.
    - `program` This directory contains the source code of the executables.
    - `NEATController.cpp` The definition of the neural network control software.
- `startgen` This directory contains the initial genomes for the design methods

## Installation
### Dependencies:
- [ARGoS3](https://github.com/ilpincy/argos3) (3.0.0-beta48)
- [argos3-epuck](https://github.com/demiurge-project/argos3-epuck) (v48)
- [demiurge-epuck-dao](https://github.com/demiurge-project/demiurge-epuck-dao) (master)

### Compiling ARGoS3-CMAES-XnES:
    $ git clone https://gitlab.com/julicot9/cmaes_xnes-argos3
    $ cd cmaes_xnes-argos3
    $ mkdir build
    $ cd build
    $ cmake ..
    $ make

Once compiled, the `bin/` folder should contain the `automode_main`
executable.

## How to use
### Run a single experiment:

### Run the design process

# Required

1. Operating System: Linux or MacOSX. Windows is not supported.

2. ARGoS3: The parallel and multi-engine simulator for heterogeneous swarm robotics ARGoS3 is required. Check the following [link](https://github.com/ilpincy/argos3 to install ARGoS3).
    * On MacOSX, you can install it directly by typing:
```
$ brew tap ilpincy/argos3 
$ brew install argos3
```

3. OpenMPI: The Message Passing Interface (MPI) library.
    * on MacOSX:
```
$ brew install open-mpi
```
    * on Linux (quick install):
```
$ sudo apt-get install openmpi-bin openmpi-common openssh-client openssh-server libopenmpi1.3 libopenmpi-dbg libopenmpi-dev
```


# Compiling

After compiling and installing ARGoS. 
From the neat directory, you need to type the following commands to build everything:
```bash
$ mkdir build
$ cd build
$ cmake ../
$ make
$ cd ..
```

# Command to launch

1. To launch the Evolutionary Process which uses NEAT and ARGoS (with the epuck robot):
    * Sequential
```
$ build/program/main config/neat.argos config/neatParams.ne config/<<startgenes>>
```
where <<startgenes>> is the stater genome file, which contains the definition of the 1st genome: for now, use ''evostickstartgenes'' (all inputs connected to outputs) or ''epuckstartgenes'' (all inputs disconnected).
   * Parallel
```
$ build/program/main config/neat.argos config/neatParams.ne config/<<startgenes>> <<n>> build/program/prog
```
where <<startgenes>> is the stater genome file, which contains the definition of the 1st genome: for now, use ''epuckstartgenes'' (all inputs connected to outputs) or ''epuckstartgenes0'' (all inputs disconnected).
where <<n>> is the nb of processes.

2. To launch a specific genome in ARGoS:
```bash
$ argos3 -c config/neat-trial.argos
```


# Create your own experiment

- If you want to create a new experiment with the current epuck's controller, which uses 8 proximity sensors, 8 light sensors, 3 ground sensors, 3 range-and-bearing sensors, a bias unit as inputs and 2 wheel actuators as outputs, you just need to create a new loop-function (which will evaluate the neural network), and possibly a new argos configuration file. Apart from those 2, You don’t need to create/change anything else.

- If instead you want to use another robot or the epuck with a different set of inputs/outputs, you will need to create your own controller, starter genome, and a new argos configuration file, in addition to the loop-function (if you want to create your own experiment).

- If you want to use just NEAT without the simulator ARGoS. You will need to modify the main program: in the main method, you will need to initialize your own experiment, then call the method launchNEAT(…) by passing your own defined method as a parameter.
launchNEAT(…) is a method that expects at least 3 arguments: the neat parameters file, the starter genome and your function that launches your experiment and evaluates an organism/network or population on this one. This method will set the evolutionary process and will call your method in which you are supposed to launch your experiment and evaluate the organisms/networks. After calling your method, launchNEAT(…) will evolve the population for the next generation.

Your method should accept only one parameter NEAT::Network& or NEAT::Population&.
-> If your method has NEAT::Network& as a parameter: you should, after launching the experiment and evaluating this network on it, return the fitness value.
-> If your method has NEAT::Population& as a parameter: you should launch the experiment for each organism and evaluate each one of them. Your method doesn’t need to return anything.

