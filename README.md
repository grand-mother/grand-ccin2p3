# CCIN2P3 Account

## Request an account
See [CCIN2P3 documentation](https://doc.cc.in2p3.fr/en/Daily-usage/account.html#request-an-account)

## Expiration date extension
See [CCIN2P3 documentation](https://doc.cc.in2p3.fr/en/Daily-usage/management.html#expiration-date-extension)

**NOTE:** Only IN2P3 accounts can apply for an extension of more than 1 year.


## Change your password, join GRAND experience

You will be able to perform the following actions yourself on your account information from the [Identity Management Portal](https://id.cc.in2p3.fr):

* modify your First and Last name,
* change or reset your password,
* request membership to scientific collaborations.
  
 ![CCIN2p3_joinCollab](https://github.com/grand-mother/grand-ccin2p3/assets/6067228/8cf477f8-3242-451f-af3b-9ce9b7753fd5)
 

[Ref CCIN2P3 doc](https://doc.cc.in2p3.fr/en/Daily-usage/account.html#profile-editing)

# CCIN2P3, useful links

[Web site](https://cc.in2p3.fr/)

[Documentation](https://doc.cc.in2p3.fr/en-index.html)

[Scheduler Slurm](https://doc.cc.in2p3.fr/en/Computing/slurm/submit.html)

[interactive session with GPU](https://doc.cc.in2p3.fr/en/Computing/slurm/examples.html#interactive-job)

[support CCIN2P3](https://support.cc.in2p3.fr/#login)

[User dashboard](https://portail.cc.in2p3.fr)

[/sps/grand/ monitoring](https://ccspsmon.in2p3.fr/users/grand.html)


# User guide for GRAND at CCIN2P3

## First use, change group

**WARNING:** 

>It is necessary to do the procedure without SSH key and only with a login / password connection

Check that your 'current group name' is well positioned on the grand experience, with command

```
newgroup -q
```

Right answer
```
current user name: <username>
current group name: grand(51760)
```

else set current group on grand


```
newgroup grand 
```

log out, wait 1 minuite, relog without **key SSH, only with login/password** and check again


## User storage

You can create a personnal directory (only one please) with huge capacity in the directory

```bash
/sps/grand
```

The best choice is your user name

```bash
mkdir /sps/grand/$USER
cd /sps/grand/$USER
```

Regularly check that your disk occupancy is not too high with this link

[/sps/grand/ monitoring](https://ccspsmon.in2p3.fr/users/grand.html)

and free space disck if necessary

## Conda env for interactive session

### Check conda installation

Test if you use the version installed for GRAND

```bash
which conda
/pbs/throng/grand/soft/miniconda3/condabin/conda
```

no ? contact JM Colley on slack ccin2p3 channel


### GRANDLIB env

With 

```bash
conda activate /sps/grand/software/conda/grandlib_2304
```
First conda grandlib env doesn't content source of grandlib, see [frequent-confusion](https://github.com/grand-mother/grand/tree/master/env/conda#frequent-confusion)

If you doesn't have a grand package yet, clone it

```bash
git clone https://github.com/grand-mother/grand.git
```

After you must initialise your grandlib package

```bash
cd /path/to/git/package/grand
source env/setup.sh
```

#### Compilation failed in other environment

If you have already tried to compile the package in an incorrect environment you must clean the compilation files already produced to start from scratch with `make clean` in `grand/src` directory

```bash
cd src
make clean
cd ..
. env/setup.sh
```

To quit conda env
```bash
conda deactivate
```

### sqlite GUI

```bash
conda activate /sps/grand/software/conda/sqlite
sqlitebrowser &

conda deactivate:
```
## Conda env for batch session

> **_NOTE:_**  A priori (because not documented) a job is submitted with current environment.


To have reproducible jobs it is preferable to explain the environment to use in the submission script. In the case of the conda environment installed for the GRAND group it is necessary to source the installation with 

```
source /pbs/throng/grand/soft/miniconda3/etc/profile.d/conda.sh
```

before doing the `conda activate`

```
conda activate <my/favorite/conda/env>
```
### Job slurm with GRANDLIB env

Your script must begin with the conda initialization, example with ```slurm_grand_job.sh```

```bash
#!/bin/sh

# init conda installed by grand experience
source /pbs/throng/grand/soft/miniconda3/etc/profile.d/conda.sh

# init GRANDLIB environment
conda activate /sps/grand/software/conda/grandlib_2304

# init GRANDLIB 
cd </path/to/my/grand/package>
source env/setup.sh

# now run your python script under GRANDLIB
...
```

```slurm_grand_job.sh``` must executable

```
chmod 755 slurm_grand_job.sh
```

and submit with slurm

```
sbatch -t 0-00:30 -n 1 --mem 2G slurm_grand_job.sh
```

sbatch answer:

```
sbatch: INFO: Account: grand
sbatch: INFO: Submission node: cca020
sbatch: INFO: Partition set to: htc
sbatch: INFO: Partition limited to one node per job.
sbatch: INFO: Time limit set to: 0-00:30 (30 minutes)
Submitted batch job 54221927
```

check your job with ```squeue```


## Apptainer image of GRANDLIB

Docker is banned in all data centers for security reasons, we use Apptainer instead.

see /pbs/throng/grand/apptainer/readme.md


### Use image interactive

```
apptainer shell --bind /pbs,/sps/grand /pbs/throng/grand/apptainer/grandlib_dev_1_2.sif

```
**IMPORTANT:**

/pbs is necessary enable graphics

else you this message error:

```
 Matplotlib is currently using agg, which is a non-GUI backend, so cannot show the figure.
```

### for admin : build apptainer image

```
apptainer build grandlib_xxxx.sif docker://grandlib/xxx:yyy
```

## Jupyter NoteBook

CCIN2P3 jupyter NoteBook link : https://notebook.cc.in2p3.fr

You can use it like a file browser or basic file editor

### Configuration

If you want access to /sps/grand and THRONG_DIR from Jupyter NoteBook add this links in your HOME

```bash
cd
ln -s /pbs/throng/grand grp_grand
ln -s /sps/grand sps_grand
```

### Conda env GRANDLIB for jupyter 

Tricky problem not solved ... 






