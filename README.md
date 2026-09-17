# Slurm UPB Cluster
How to use slurm in UPB Cluster and in general

- [Slurm UPB Cluster](#slurm-upb-cluster)
    - [Info server](#info-server)
  - [1) Conectare server](#1-conectare-server)
  - [2) Logare pe un nod:](#2-logare-pe-un-nod)
  - [3) Submitere job](#3-submitere-job)
  - [4) Check running jobs](#4-check-running-jobs)
  - [5) Cancel job](#5-cancel-job)
  - [6) Rulare Multi-Core ale Joburilor](#6-rulare-multi-core-ale-joburilor)
  - [7) Rulare multi-task/multi-process](#7-rulare-multi-taskmulti-process)
  - [8) FOLOSIRE GPU](#8-folosire-gpu)
  - [9) Listeaza gpu-urile si partitiile](#9-listeaza-gpu-urile-si-partitiile)

### Info server
Vom folosi două tipuri de cozi pentru folosirea a două tipuri de resurse:

CPU:
- haswell;
- nehalem;
GPU:
- ucsx;
- xl;

## 1) Conectare server

`ssh gabriel.pirlogeanu@fep8.grid.pub.ro -X -o ServerAliveInterval=100`

NOTE: give your UPB user ID. Va trebui sa te loghezi prin authenticator cu un cod etc.



## 2) Logare pe un nod:

`srun --pty -p <partiție> bash` 

E.g. `srun --pty -p haswell bash` (haswell e una din partitii)

## 3) Submitere job
`sbatch [-t <timp-rulare>] -p <partiție> <cale-script>`

E.g. 

```
> cat batch.sh
#!/bin/bash
hostname
> sbatch --time 01:00:00 -p haswell ./batch.sh
Submitted batch job <job_id>
> cat slurm-<job_id>.out
haswell-wn39.grid.pub.ro
```

(create a dummy batch.sh file with hostname in it idk and then do `cat slurm-<id>.out`)

## 4) Check running jobs

`squeue -u gabriel.pirlogeanu` (give <user_id>)

E.g.

```
cat batch.sh
#!/bin/bash

sleep 10000
[gabriel.pirlogeanu@fep10 ~]$ sbatch -p haswell batch.sh
Submitted batch job 263196
[gabriel.pirlogeanu@fep10 ~]$ squeue -u gabriel.pirlogeanu
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
            263196   haswell batch.sh gabriel.  R       0:06      1 haswell-wn40
            263195   haswell batch.sh gabriel.  R       0:27      1 haswell-wn39
```

## 5) Cancel job
`scancel <job_id>`

E.g.
```
[gabriel.pirlogeanu@fep10 ~]$ squeue -u gabriel.pirlogeanu
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
            263196   haswell batch.sh gabriel.  R       0:06      1 haswell-wn40
            263195   haswell batch.sh gabriel.  R       0:27      1 haswell-wn39
[gabriel.pirlogeanu@fep10 ~]$ scancel 263196
[gabriel.pirlogeanu@fep10 ~]$ squeue -u gabriel.pirlogeanu
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
            263195   haswell batch.sh gabriel.  R       0:45      1 haswell-wn39

```

## 6) Rulare Multi-Core ale Joburilor

`srun -p nehalem -c 8 --pty bash`

Opțiunea `-c` controlează numărul de core-uri la care să fie limitat jobul.

Fiecare job pornit are o limită predefinită de 2GB de memorie per core. Pentru a folosi o cantitate diferită de memorie per nucleu este necesară folosirea opțiunii `--mem-per-cpu`.

## 7) Rulare multi-task/multi-process

```
[sergiu.weisz@fep8 mpi]$ sbatch -p haswell -n 5 run_hello.sh
```

Am folosit opțiunea `-n` 5 pentru a porni 5 procese MPI. Toate procesele vor fi distribuite pe o singură stație. Pentru a rula procese pe mai multe noduri, folosim opțiunea `-N <numar noduri>`.

## 8) FOLOSIRE GPU

```
srun -p xl --gres gpu:1 --pty bash
---
SAU
---
srun -p xl --gres gpu:tesla_p100:1 --pty bash
```

Opțiunea `--gres` permite utilizatorului să ceară acces la un număr de GPU-uri per job folosind opțiuena de forma `--gres gpu:<număr gpu-uri>` sau `--gres gpu:<model gpu>:<număr gpu-uri>`.

## 9) Listeaza gpu-urile si partitiile

```
[gabriel.pirlogeanu@fep10 ~]$ sinfo -o "%10P %20N %10c %10m %25f %20G "
PARTITION  NODELIST             CPUS       MEMORY     AVAIL_FEATURES            GRES   
dgxa100    dgxa100-ncit-wn[01-0 256        2063510    (null)                    gpu:tesla_a100:8
dgxh100    dgxh100-precis-wn[01 224        1998908+   (null)                    gpu:tesla_h100:8
h200       ucsc-precis-h200-wn[ 256        2321395+   (null)                    gpu:nvidia_h200:8
haswell*   haswell-wn[29-42]    32         127309     (null)                    (null) 
hd         xl675dg10-wn175      96         773225     (null)                    gpu:tesla_a100:10
ml         sprmcrogpu-wn[140-14 112        128224     (null)                    gpu:tesla_a100:2
sprmcrogpu sprmcrogpu-wn13      64         515093     (null)                    gpu:rtx_2080ti:8
ucsx       ucsx-ncit-gpu-wn100  64         257158     (null)                    gpu:tesla_a100:3
xl         xl270-wn[161-162]    56         257138     (null)                    gpu:tesla_p100:2
```

## 10) Analizeaza job-uri curente sau din trecut

- Running jobs:
`sstat --jobs=your_job-id`

Variables: avecpu, averss, avevmsize, jobid, maxrss, maxvmsize, ntasks

More exact:
`sstat --jobs=your_job-id -a --format=jobid,avecpu,maxrss,ntasks`

- Past jobs:
`sacct --jobs=your_job-id`

## 11) Controleaza job-uri

Suspenda job:

`scontrol suspend <jobid>`

Resume job:

`scontrol resume <jobid>`

**NOTE**: suspend - resume

Hold job:

`scontrol hold <jobid>`

Release job:

`scontrol release <jobid>`

**NOTE**: hold - release

## 12) Vizualizeaza job

```
# Output to console
$ scontrol show job job_id

# Streaming output to a textfile
$ scontrol show job job_id > outputfile.txt

# Piping output to Grep and find lines containing the word "Time"
$ scontrol show job job_id | grep Time
```