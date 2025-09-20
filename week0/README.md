# Week0 of RISC-V Reference SoC Tapeout Program by VSD - Tools Installation and Getting Started

## Summary of Getting started with Digital VLSI SOC Design and Planning
The VLSI (Very Large Scale Integration) System on a Chip (SOC) design process is a multi-step workflow. It typically begins with a high-level specification (Specs) or C model, which acts as the golden reference model for the design. The C model is used for functional verification with a C-language testbench, establishing a baseline.

Next, the design is implemented as a Register-Transfer Level (RTL) soft copy using a Hardware Description Language (HDL) like Verilog. This RTL is then broken down into components like the processor and peripherals, which are individually synthesized into a Gate Level Netlist. At this stage, the design is integrated, and the final layout is created, a process that includes floorplanning, placement, clock tree synthesis (cts), and routing. The final output is a GDSII (Graphic Data System II) file, a database file format that represents the two-dimensional layout of an integrated circuit.

Throughout the process, each stage is verified against the previous stage to ensure functional equivalence. For instance, the Gate Level Netlist is checked against the RTL, and the RTL is checked against the C model. The final SOC is a complex integration of digital and analog components, and its performance is evaluated against the initial specifications, often involving clock frequency targets.

## Tools installation

### 1. Yosys 
```bash
sudo apt-get update
git clone https://github.com/YosysHQ/yosys.git
cd yosys
sudo apt install make
sudo apt-get install build-essential clang bison flex \
 libreadline-dev gawk tcl-dev libffi-dev git \
 graphviz xdot pkg-config python3 libboost-system-dev \
 libboost-python-dev libboost-filesystem-dev zlib1g-dev
make config-gcc
make
sudo make install 
```
### 2.Iverilog
```bash
sudo apt-get update
sudo apt-get install iverilog
```
### 3.gtkwave
```bash
sudo apt-get update
sudo apt install gtkwave 
```
### 4.OpenSTA
```bash 
git clone https://github.com/parallaxsw/OpenSTA.git  
cd OpenSTA
docker build --file Dockerfile.ubuntu22.04 --tag opensta .  //build with docker
```
### 5.ngspice
```bash
tar -zxvf ngspice-37.tar.gz
cd ngspice-37
mkdir release
cd release
../configure --with-x --with-readline=yes --disable-debug
make
sudo make install 
```
### 6.magic
```bash
sudo apt-get install m4
sudo apt-get install tcsh
sudo apt-get install csh
sudo apt-get install libx11-dev
sudo apt-get install tcl-dev tk-dev
sudo apt-get install libcairo2-dev
sudo apt-get install mesa-common-dev libglu1-mesa-dev
sudo apt-get install libncurses-dev
git clone https://github.com/RTimothyEdwards/magic
cd magic
./configure
make
make install 
```
### 7.OpenLane
```bash
git clone https://github.com/The-OpenROAD-Project/OpenLane
cd OpenLane
make
make test 
```

