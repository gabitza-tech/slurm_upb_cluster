# Slurm UPB Cluster
How to use slurm in UPB Cluster and in general

## 1) Conectare server

`ssh gabriel.pirlogeanu@fep8.grid.pub.ro -X -o ServerAliveInterval=100`

NOTE: give your UPB user ID. Va trebui sa te loghezi prin authenticator cu un cod etc.

# Info server
Vom folosi două tipuri de cozi pentru folosirea a două tipuri de resurse:

CPU:
- haswell;
- nehalem;
GPU:
- ucsx;
- xl;

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


## 5) Cancel job
`scancel <job_id>`

E.g.
```

```
