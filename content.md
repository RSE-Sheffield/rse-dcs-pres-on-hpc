# High-Performance Computing at the University of Sheffield

<div class="right">
    <img src="images/rse-logoonly-stroke.png" width="50%" style="padding: 0 15px; float: right;"/>
</div>

Will Furnass

Research Software Engineering team, University of Sheffield

[https://rse.shef.ac.uk/rse-dcs-pres-on-hpc/](https://rse.shef.ac.uk/rse-dcs-pres-on-hpc/)

---
## Outline

 1. What is HPC? Why bother?
 1. HPC at UoS
 1. Using HPC
 1. Further resources

---
## Personal machines for research

<img src="images/Lenovo_ThinkPad_T420_Ubuntu_Linux.jpeg" alt="Lenovo Thinkpad" width="60%" />

  - Edit and run code in one place
  - ~4 cores - some parallelism
  - Full control!

but...

---
## Limitations of personal machines

  - Limited RAM
  - Basic CPU
  - Limited number of cores
  - Limited, fragile storage
  - Modest GPU
  - Limited network connection
---
## Limitations of personal machines

  - How to distribute work *between* laptops?
  - How to run a series of tasks overnight?
      - "1am: Check if run 3 finished & start run 4"
      - "3am: Check if run 4 finished & start run 5"
      - "5am: Check if run 5 finished & start run 6"

---
## HPC: how is it different?

'HPC': a computer cluster with:
  - Computing resources
    - many nodes
    - each with many cores, much RAM, maybe GPUs
    - connected by fast networking
  - Storage
    - both shared and per-node
    - resilient and fast
  - Job management
    - Queue up jobs to run over a week
  - Command-line as default interface
  - Linux OS + optimised research software

---
## UoS Clusters

- Bessemer: 
    - Newest hardware
    - Best for single-node jobs
- ShARC: 
    - Good for multi-node jobs as has high-bandwidth, low-latency interconnects

---
## DCS HPC resources

  - Majority of nodes **public** (free at point of use)
  - But DCS has some **private nodes** in Bessemer and ShARC:
    - Non-std hardware specs
    - Less contention (sometimes!)
---
### Bessemer DCS node specs 

- 8x nodes with each with 
  - **4x NVIDIA V100 GPUs**
  - Fast NVLink interconnects between GPUs
  - 192 GB RAM
      [https://docs.hpc.shef.ac.uk/en/latest/bessemer/groupnodes/](https://docs.hpc.shef.ac.uk/en/latest/bessemer/groupnodes/)

---
### ShARC DCS group nodes
- 1x node with 
  - **8x NVIDIA P100 GPUs** (NB 1x GPU currently faulty)
  - Fast NVLink interconnects between GPUs
  - 512GB RAM
- 8x nodes each with 
  - **768GB RAM** 
  - 5 with 1TB SSDs
- 4x nodes each with 
  - 32 cores
      [https://docs.hpc.shef.ac.uk/en/latest/sharc/groupnodes/](https://docs.hpc.shef.ac.uk/en/latest/sharc/groupnodes/)


---
## Cluster structure

<img src="images/cluster-diag-plain.svg" alt="Cluster diagram" width="100%" />

---
## Jupyter

Can also run Jupyter Notebooks on cluster!

<div style="text-align: center">
    <a href="https://github.com/RSE-Sheffield/hi-perf-ipynb/blob/master/tutorials/02-multiprocessing.ipynb">
        <img src="images/jupyter.png" alt="Example Jupyter notebook" width="80%" align="center" />
    </a>
</div>


Ask for more info

---
## Moving data to/from HPC

  - SSH-based methods are your friends here (`rsync`, `scp`, `sftp`)
  - Or 
      - Use a storage area directly accessible to both your local machine and HPC?
      - Just use HPC?

---
## Storage

Location            | Shared? | Quota  | Backups? | Multi-HPC
------------------- | :-----: | -----: | :------: | :-------:
`/home/$USER`       | ✓       | 10 GB  | ✓        | ✓
`/data/$USER`       | ✓       | 100 GB | ✓        | ✓
`/fastdata/$USER`   | ✓       | -      | ✗        | ✓'
`/scratch`          | ✗       | -      | ✗        | ✗
`/shared/$PROJNAME` | ✓       | 10 TB  | ✗        | ✓

---
## Storage

Location            | Remote access? | Speed | Suited to       
------------------- |:-------------: |:----- |:---------------
`/home/$USER`       | SSH            | >     | Pers data       
`/data/$USER`       | SSH            | >     | Pers data       
`/fastdata/$USER`   | SSH            | >>>   | Tmp big files   
`/scratch`          | -              | >>>   | Tmp small files 
`/shared/$PROJNAME` | SSH + CIFS     | >     | Proj files      




---
## Running jobs

- Users submit jobs to a **job scheduler** 
    - e.g. Slurm (Bessemer) or SGE (ShARC)
    - A **distributed resource manager**
    - Not intuitive!
    - V. powerful
--
- Request 
    - **Interactive** or **batch** job
    - Run time (e.g. 2h or 4d)
    - Computational resources (cores, RAM, GPUs)
    - Access to private resources
    - Notifications
--
- Type of job
  - Interactive sessions (if resources are available)
  - Batch jobs (submit job to a queue)

---
## Example interactive session

```
[me@mylaptop ~]$ ssh te1st@bessemer.sheffield.ac.uk
...
[te1st@bessemer-login1 ~]$ srun \
  --partition=dcs-gpu-test \
  --account=dcs-res \
  --cpus-per-gpu=4 \
  --mem-per-cpu=2G \
  --gpus=1 \
  --pty \
  /bin/bash

[te1st@bessemer-node030 ~]$ ./my_simulation_program --num-cores=4
...
```

---
## Example batch job script (Bessemer/Slurm)

Create a shell script, `my-job-script.slurm`:

```bash
#!/bin/bash
#SBATCH --partition=dcs-gpu
#SBATCH --account=dcs-res
#SBATCH --cpus-per-gpu=4
#SBATCH --mem-per-gpu=2G
#SBATCH --gpus=4
#SBATCH --mail-user=me@sheffield.ac.uk

./my_simulation_program --num-cores=16
```


Copy this file to Bessemer
then log on to Bessemer and 
submit this to Slurm:

```console
[me@mylaptop ~]$ ssh te1st@bessemer.sheffield.ac.uk
[te1st@bessemer-login1 ~]$ sbatch my-job-script.slurm
```
Now go home for dinner!

---
## After submitting a job

You can then:
 - Wait for an email notification
 - Check status (running/queueing)
 - Cancel/amend job

---
## Resource estimation

 1. Run short test jobs
 1. View resource utilisation
 1. Extrapolate
 1. Submit larger jobs

---
## Software on UoS HPC

### Centrally-installed, optimised software

* Compilers, libraries, apps, dev tools etc
* Activate a package by **loading a modulefile** e.g.

    ```sh
    module use $MODULENAME
    ```

---
Where, for e.g. cuDNN, `$MODULENAME` could be one of:

```
libs/cudnn/4.0/binary-cuda-7.5.18
libs/cudnn/5.1/binary-cuda-7.5.18
libs/cudnn/5.1/binary-cuda-8.0.44
libs/cudnn/6.0/binary-cuda-8.0.44
libs/cudnn/7.0/binary-cuda-8.0.44
libs/cudnn/7.0/binary-cuda-9.1.85
...
```

---
## Software on UoS HPC

### Manage your own software

Several options:

  - Install non-optimised binary packages in e.g. your home directory 
      - *Conda*
  - Build optimised software stacks from source
      - *Spack*, *EasyBuild*
  - Run containers
      - *Singularity* - similar to *Docker*

All useful for e.g. provisioning/using complex Deep Learning software stacks!

---
## Optimisation and parallelisation

- Laptop may be *faster* than single-core job on HPC:
    - CPUs in servers run at lower clock speeds
    - `.exe` may not exploit advanced CPU features

- Performance often comes from >=1 of:
    - **Optimising** for **CPU architecture**
    - **CPU parallelism** (multiple cores, multiple nodes)
    - **Accelerators** (GPUs, TPUs, Xeon Phi etc)

---
### Optimising for CPU architecture

  - In Bessemer and ShARC the CPUs support 
    - **hardware vectorisation** (same instruction applied to multiple elements in memory)
    - **fused add-multiply** (useful for matrix multplication)
  - Either use pre-compiled libraries that can dynamically use these
      - e.g. Intel Math Kernel Library (MKL)
  - or compile to produce builds optimised for those CPUs

---
### CPU parallelism

(At least) 5 flavours:

#### Shared memory

- Single node
- Multiple CPU cores
- Typically 1 thread per core
- Thread-local and shared variables
- Many applications do this via OpenMP and/or Intel MKL

---
#### Distributed memory (single node)

- Multiple CPU cores
- Typically 1 process per core
- Separate address spaces
- Data (and code?) passed between processes
- e.g. `joblib` w/ multiprocessing or `ipyparallel`; MATLAB parfor; R `parallel`/`foreach`

---
#### Distributed memory (multiple nodes)

- Multiple CPU cores per node (symmetric?)
- Typically 1 process per core
- Separate address spaces per process
- Data (and code?) passed between processes
    - within a node
    - between nodes
- V. fast interconnects between nodes
- Facilitated by 
    - MPI (API + software for exploiting fast interconnects)
    - Apps/libs that understand MPI (`ipyparallel`, `PETSc`, MATLAB DCE)

---
#### Task arrays

- Set of near identical tasks
- Embarrassingly parallel
- Scheduled separately 
- Which then packs out its schedule with them!
- Great for sensitivity analyses

---
#### Accelerators, specifically CUDA

- Massive data parallelism
- Very effective for linear-algebra-heavy ops
    - ML, DL
- Can either write low-level code in CUDA
- Or use higher-level libs that speak CUDA
    - Tensorflow, PyTorch etc for DL

---
#### Other options

High-level APIs for working with large datasets, possibly out of core:

 - Spark
 - Dask

---
## Going bigger!

- Potential issues
    - Jobs too big / queue times too long for ShARC?
    - Want newer GPUs/processors?
---
- Options
    - **JADE**: Tier 2 HPC facility for Deep Learning
        - 22x DGX-1 systems: 22x 8x NVIDIA V100 cards (NVLINK between GPUs in nodes)
        - To see [£5.5M upgrade in 2020](https://www.hpcwire.com/off-the-wire/oxford-wins-5-5-million-epsrc-funding-for-hpc-will-lead-jade-2/) (**JADE 2**)
    - **Bede**: new N8 Tier 2 HPC facility for distributed DL/ML
        - 32x IBM AC922 nodes (2x POWER9 CPU; 4x V100 GPU; NVLINK between GPUs and CPUs)
        - 4x IBM IC922 'inference' nodes with T4 GPUs 
        - 100Gbps Infiniband EDR interconnects
        - Better suited to hybrid CPU+GPU codes and scaling to multiple nodes than JADE
---
- Options (continued)
    - **Other Tier 2 facilities** (https://www.hpc-uk.ac.uk/facilities/)
    - Tier 1 HPC facility: **Archer**
    - **Cloud** (AWS, Azure, GCP etc)
        - Alces Flight - traditional HPC in the cloud

---
## Learning more / getting help

* Docs: [https://docs.hpc.shef.ac.uk](https://docs.hpc.shef.ac.uk) (not a tutorial)
    * For DCS nodes: [https://docs.hpc.shef.ac.uk/en/latest/sharc/groupnodes/](https://docs.hpc.shef.ac.uk/en/latest/sharc/groupnodes/)
* **Workshops**
    * RSE team runs various workshops on fundamentals:
        * UNIX shell, Git, Python/R/MATLAB, relational databases...
    * and more advanced topics:
        * multithreading/multiprocessing, CUDA, deep learning...
    * IT Services also offer training in C/C++, Fortran, Python, MATLAB and HPC

---
## Learning more / getting help

* **IT Services' helpdesk**
* **Talks**
    * LunchBytes talks
* **Code Clinic**
    * Book an appointment to get help with a coding issue
* **Hire an RSE** to help with your project(s)!
    * Either as part of a grant proposal
    * Or just for a few days

---
For more info (inc. **mailing list** and events schedule) see [https://rse.shef.ac.uk/](https://rse.shef.ac.uk/).

<div class="middle">
    <div class="center">
        <img src="images/rse-events.png" width="80%" />
    </div>
</div>

---
## The RSE team

* 13.5 RSEs
* Team kick-started by 2x EPSRC RSE fellowships
* Based in Computer Science 
* but work closely with IT Services
* Some current and recent projects:
  * High-performance **agent-based modelling** (CUDA)
  * Deep learning and workflows for **NLP**
  * MRI **image alignment** (registration) software (C++/PETSc)
  * Agile **web apps** for visualising datasets (R/Shiny)
  * Augmenting **cell modelling** software (C++)

---
## Getting in touch

<i class="fa fa-globe fa-lg"></i>&nbsp;[https://rse.shef.ac.uk](https://rse.shef.ac.uk)

<i class="fa fa-envelope fa-lg"></i>&nbsp;[rse@sheffield.ac.uk](maito://rse@sheffield.ac.uk)

<i class="fa fa-github fa-lg"></i>&nbsp;[@RSE-Sheffield](https://github.com/RSE-Sheffield/RSE-Sheffield.github.io)

<i class="fa fa-twitter fa-lg"></i>&nbsp;[@RSE_Sheffield](https://twitter.com/rse_sheffield)
