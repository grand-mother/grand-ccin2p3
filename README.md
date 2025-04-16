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
id -gn
```

Right answer
```
grand
```

else set current group on grand


```
newgroup grand 
```

log out, wait 10 minutes, relog without **key SSH, only with login/password** and check again


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
echo $CONDA_EXE
/pbs/throng/grand/soft/miniconda3/condabin/conda
```

no ? contact JM Colley on slack ccin2p3 channel


### GRANDLIB env

With 

```bash
conda activate /sps/grand/software/conda/grandlib_2304
```
First conda grandlib env doesn't content source of grandlib, see [frequent-confusion](https://github.com/grand-mother/grand/tree/master/env/conda#frequent-confusion)

If you doesn't have a grand package yet, clone it, checkout your favorite branch

```bash
git clone https://github.com/grand-mother/grand.git
cd grand
git checkout <my_favorite_branch>
```

After you must initialise your grandlib package

```bash
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

# Remote development with VS Code


**UNDER TESTING**

Use case, GRANDLIB development with VS Code.

## Prerequisites
* a wired connection is essential
* connection SSH without password/passphrase
* conda available in your CCIN2P3 setup
  * By default is the case, your .profile source group_profile who defined conda GRAND installation
* VS Code is updated on your local machine, specific extension below

## Initializing a GRANDLIB package for VS Code

### What do we need for VS Code ?

Two configurations files for the initialisation of VS Code in Python mode:
 * .vscode/settings.json
 * .env

 Note that this is a hidden file and a file in a hidden directory. 
 Content of `settings.json` file

```console
{
    "python.envFile":	"${workspaceFolder}/.env"
}
```

It's the minimal setting for Python, it's the main file of configuration for VS Code.  Please, read VS Code documentation to learn more

The hidden file `.env` in root git  package. It looks like an init `bash` file.

```console
# file env for VS Code 
GRAND_ROOT=/sps/grand/software/test_vscode/grand
PYTHONPATH=/sps/grand/software/test_vscode/grand:$PYTHONPATH
PATH=./quality:./examples/dataio:./scripts:$PATH
```

**Note:**

If you modify this files and the changes are not taken into account, **you must restart VS Code**.


**VS Code reference:**
* [Settings json file](https://code.visualstudio.com/docs/configure/settings#_settings-json-file)
* [Settings python](https://code.visualstudio.com/docs/python/settings-reference#_general-python-settings)
* [.env file](https://code.visualstudio.com/docs/python/environments#_environment-variable-definitions-file)

### How to get this setup

 **By hand**

Copy the files above and adapt `.env` to your installation, respecting the absolute paths, they are important.
Please don't commit/push it in grand_mother grand repository your `.env` because it's your personal configuration.

**Automatic configuration with env/setup.sh**

2 cases 
* you start with a new GRANDLIB package from branch dev
* you start with an "old" personal branch alredayd installed at CCIN2P3

#### Case "new" project from dev branch (after 21 march 2025)

How lucky you are! You have to just initialize package as usual

```bash
conda activate /sps/grand/software/conda/grandlib_2304
source env/setup.sh 
```

 This file `.env` is created only if isn't exist, so you can personalise it as you want after creation. But, please don't commit/push it in grand_mother grand repository because it's your personal configuration.

So, for the first env/setup.sh, you must have "Create VS Code file environment." in log.



```console
...
PYTHON   _core.abi3.so
Create VS Code file environment.
==============================
Download data model (~ 1GB) for GRAND, please wait ...
...
```
else
```console
...
make: rien à faire pour « all ».
PASSED : VS Code .env file already exits.To re-create a default one, remove it and restart 'source env/setup.sh'
==============================
Skip download data model
...
```

#### Case "old" personal branch (git clone before 21 march 2025)

An update of `env/setup.sh` will configure correctly initialization file for VS Code. So we will merge branch `dev_vs_code` to your personal branch in CCIN2P3.

```bash
ssh xxx@cca.in2p2.fr
cca>cd /path/to/my/package/grand
cca>git status
on the branch dev_xxx
...
git fetch
git merge origin/dev_vs_code
```

May be you can have conflict in .gitignore, easly to solve I think. Edit .gitignore and keep all rules, save, exit and commit

```bash
git commit -a -m "solve conflict"
```


```bash
conda activate /sps/grand/software/conda/grandlib_2304
source env/setup.sh 
```


## VS Code extensions 

Launch VS Code and install this list of extensions:

Ctrl+Shift+x and search/install this name package

* Conda Env Activator
* Jupyter
* Jupyter Notebook Renderers
* Markdown+Math
* Python
  * Pylance
  * Python Extension Pack
* Remote - SSH: Editing Configuration Files
* Remote Development
  * Remote - SSH
  * Remote - Tunnels
  * WSL
  * Dev Container
* Remote Explorer
 

## VS Code in remote mode and X11


Crtl+Shift+p , enter `>SSH` and select in proposition list
* Remote SSH: add new SSH Host

Enter : `ssh xxx@cca.in2p2.fr` and OK


now enable X11 for graphism

Crtl+Shift+p , enter `>open SSH` and select in proposition list
* Remote SSH: Open SSH configuration file
* then `/home/xxx/.ssh/config`

And add `ForwardX11 yes` in cca Host, like this
```bash
Host cca.in2p3.fr
     HostName cca.in2p3.fr
     ForwardX11 yes
     User xxx
```

Save and quit VS Code

## VS Code with remote GRANDLIB package

### Define project

Launch VS Code
File>Open recent>  choice .....[SSH:cca.in2p3.fr]

File>Open folder

put the /path/to/my/package/grand of GRANDLIB package we have just configured.

### Define conda env

Crtl+Shift+p , enter `>python` and select in proposition list
* Python: Select interpreter

enter `grandlib` and select in proposition list
* Python 3.9.16 ('grandlib_2304')

### Check X11 graphism

View>Terminal

In the terminal enter : `python -m tkinter`

Tk windows must appear quickly

### Check GRANDLIB code

#### Coverage tests

View>Terminal

In the terminal enter : `grand_quality_test_cov.bash`

#### Scripts with plot

In explorer of package open file  scripts>plot_noise.py

Run it with icon "play" **|>**  at top right of the window.

After ~ 20 s a plot appears.

**Note:**

If you launch this script in "classical" SSH session, the plot appear more quickly, the compromise for long scripts and/or with plots is to launch them from a classic SSH terminal. **But you get a very powerful IDE**

## VS Code basic coding tips

* F12 : Go to reference of selected function, class, module
* Browser class and function in file or chapiter with markdown document
  * in Explorer bar, select "OUTLINE"
* Crtl+Shift+F : search string in all project
* F2 : rename function/class and update all occurences
  * selection string and F2
* Crtl+B  (like Firefox bookmark bar): Hide/Add primary side bar (Explorer, GIT, Extensions, ...)


## VS Code and Jupyter notebook

### Kernel selection

* open notebook  yyy.ipynb
* top, right of the windows, click on "Select Kernel"
* choice 'grandlib_2304'
* run cell. First run cell is slow (initialise Kernel ?...) but the other run cell are more responsive.

### Interactive plot

Add 
`%matplotlib widget`
at the top of your notebook

**Note:**
Interactive plot are very slow.

## CCIN2P3 JupyterLab

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

