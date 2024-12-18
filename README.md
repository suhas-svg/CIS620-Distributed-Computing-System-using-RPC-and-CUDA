# Distributed Computing with RPC, Threads, and CUDA Toolkit

## Project Overview

This project demonstrates a distributed system application using **Remote Procedure Call (RPC)** protocol, **POSIX threads**, and **CUDA Toolkit** to compute the sum of quadruple roots of an array or linked list. The system consists of a **client** program running on a Linux workstation and **servers** running on pre-selected Linux workstations. The client distributes the computation across two servers with the lowest system loads, as determined by a load-balancing mechanism.

### Key Features
- **Distributed Computation**: The client distributes tasks to two servers with the lowest load.
- **GPU Acceleration**: CUDA-enabled servers perform high-performance computations.
- **Data Structures**: Supports both array-based and linked list implementations for computation.
- **Fault-Tolerant Design**: Includes error handling for memory allocation, RPC calls, and server communication.

---

## Project Files

### 1. `ldshr.x`
An **RPC interface definition file** that specifies the structure of the RPC services using the ONC RPC protocol. This file defines:
- **Input structs**:
  - `input`: Contains parameters `N`, `M`, `S1`, and `S2` for GPU computations.
  - `LinkedList`: A linked list structure consisting of `Node` elements for list-based computations.
- **RPC Program**:
  - `getload`: Retrieves the 5-minute load average of a server.
  - `sumqroot_gpu`: Computes the sum of quadruple roots using CUDA for arrays.
  - `sumqroot_lst`: Computes the sum of quadruple roots using a linked list.

### 2. `ldshr.c`
The **client-side program** that:
1. Fetches load averages from five pre-defined servers: `arthur`, `bach`, `brahms`, `chopin`, and `degas`.
2. Selects the two servers with the lowest system load.
3. Distributes the computational workload using two threads:
   - **`-gpu` option**: Splits an array into two parts and computes the quadruple root sum on the selected servers using GPU.
   - **`-lst` option**: Reads data from a file (`datafile`) into two linked lists, computes the quadruple root sum of each list on the servers, and aggregates the results.

### 3. `ldshr_svc_proc.c`
The **server-side RPC implementation**:
- **`getload`**: Fetches the 5-minute load average using the system call `getloadavg`.
- **`sumqroot_gpu`**: Performs GPU-based computation by:
  - Initializing an array with input parameters (`N`, `M`, `S1`, `S2`).
  - Using CUDA kernels to compute quadruple roots (`map`) and sum them (`reduce`).
- **`sumqroot_lst`**: Processes a linked list by:
  - Applying the `map` function to compute the quadruple root of each node.
  - Using the `reduce` function to sum the results.

### 4. `reduction.cu`
A **CUDA program** that performs GPU-accelerated computations:
- **`map` kernel**: Computes the quadruple root of each element in an array.
- **`reduce` kernel**: Summarizes the array using shared memory for efficient intra-block processing.
- **`sumqroot` function**: Manages memory allocation, kernel launches, and data transfer between host and GPU.

### 5. `makefile`
Automates the build process for the project:
- Compiles the client (`ldshr`) and server (`ldshr_svc`) executables.
- Generates intermediate files (e.g., `ldshr.h`) using `rpcgen`.
- Links GPU-enabled components with the CUDA runtime library.

### 6. `datafile.txt`
A sample input file containing floating-point numbers for linked list-based computation.

---

## Setup and Execution

### Prerequisites
- Linux environment with **CUDA Toolkit** installed.
- ONC RPC tools (`rpcgen`, `rpcbind`) installed.
- **POSIX threads** (`-lpthread`).
- At least five Linux workstations with accessible hostnames.

### Steps to Run

#### 1. Compile the Project
Run the following command to compile the client and server programs:
```bash
make


#### 2. Start the Servers
Open **five terminals**, one for each server, and run the following command on the respective machines:
```bash
./ldshr_svc
```

#### 3. Run the Client
Open a **sixth terminal** and run one of the following commands based on the computation type:

- **Array-based GPU computation**:
  ```bash
  ./ldshr -gpu N M S1 S2
  ```
  Example:
  ```bash
  ./ldshr -gpu 20 5 17 23
  ```
  - `N`: Power of two determining array size (e.g., `2^N` elements).
  - `M`: Mean value for exponential distribution.
  - `S1`, `S2`: Seed values for random number generation.

- **Linked list-based computation**:
  ```bash
  ./ldshr -lst datafile
  ```
  Example:
  ```bash
  ./ldshr -lst datafile
  ```

---

## Example Outputs

### Case 1: GPU Computation
Command:
```bash
./ldshr -gpu 20 5 17 23
```
Output:
```
arthur: 2.30 bach: 3.3 brahms: 3.8 chopin: 0.50 degas: 1.27
(executed on chopin and degas)
Result: 1421129.93
```

### Case 2: Linked List Computation
Command:
```bash
./ldshr -lst datafile
```
Output:
```
arthur: 1.30 bach: 5.5 brahms: 2.8 chopin: 2.27 degas: 0.9
(executed on arthur and degas)
Final sum of results: 7.40
```

---

## Testing and Debugging

### Known Issues and Fixes
- **Thread Result Summing to `0.00`**: This occurred due to improper memory handling in the client (`ldshr.c`). The issue was fixed by appropriately freeing allocated memory and correctly aggregating thread results.

---

## Project Details

### Authors
- **Your Name**
- **Your Team Members**

### Status
- **COMPLETELY WORKING**

---

## Clean Up
To remove compiled files, run:
```bash
make clean
```
This will delete all executables, object files, and intermediate files generated during the build process.
```
