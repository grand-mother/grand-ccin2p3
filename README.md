# CCIN2P3

[Web site](https://cc.in2p3.fr/)

[Documentation](https://doc.cc.in2p3.fr/en-index.html)

[Scheduler Slurm](https://doc.cc.in2p3.fr/en/Computing/slurm/submit.html)

[interactive session with GPU](https://doc.cc.in2p3.fr/en/Computing/slurm/examples.html#interactive-job)

[support CCIN2P3](https://support.cc.in2p3.fr/#login)

[User dashboard](https://portail.cc.in2p3.fr)

# User guide for GRAND at CCIN2P3

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


## Jupyter NoteBook

CCIN2P3 jupyter NoteBook link : https://notebook.cc.in2p3.fr

### Configuration

If you want access to /sps/grand and THRONG_DIR from Jupyter NoteBook add this links in your HOME

```bash
cd
ln -s /pbs/throng/grand grp_grand
ln -s /sps/grand sps_grand
```

### Conda env for jupyter 

TODO

## Conda env for interactive/batch session

### Check conda installation

Test if you use the version installed for GRAND

```bash
which conda
/pbs/throng/grand/soft/miniconda3/condabin/conda
```

no ? contact JM Colley on slack ccin2p3 channel


### GRANDLIB env

```bash
conda activate /sps/grand/software/conda/grandlib_2304

conda deactivate
```

### sqlite GUI

```bash
conda activate /sps/grand/software/conda/sqlite
sqlitebrowser &

conda deactivate:
```
## Conda env for batch session

In your script slurm you must source our conda installation script like this

```
source /pbs/throng/grand/soft/miniconda3/etc/profile.d/conda.sh
```

before activate conda env

```
conda activate <my/favorite/conda/env>
```
### job slurm with GRANDLIB env

Your script must begin with this initialization

```bash
source /pbs/throng/grand/soft/miniconda3/etc/profile.d/conda.sh
conda activate /sps/grand/software/conda/grandlib_2304
cd </path/to/my/grand/package>
source env/setup.sh

# now run your python script under GRANDLIB
...
```

## Apptainer image of GRANDLIB

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




