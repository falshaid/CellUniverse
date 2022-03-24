Cell Universe
=============

Tracks the movement of cells from a video source. Currently the code has only been verified to work in 2D, with bacteria-shaped cells. More to come.

Once you clone the repo onto your machine, you should test if Cell Universe is working on your system by installing the requirements and running the script `./regression-test-all.sh`.

Prerequisites:
-----------
Requires Python 3.7+.
https://www.python.org/getit/

Requires Pip.
https://pip.pypa.io/en/stable/quickstart/

Quickstart:

1. Install Pipenv
```bash
$ pip install pipenv
```

2. Install required packages using Pipenv
```bash
$ pipenv install
```
This will create a virtual environment with all the required Python packages

3. Activate Pipenv environment
```bash
$ pipenv shell
```

3. Run Cell Universe on an example video (look inside the `run.sh` script to see the command used).
```bash
$ cd examples/canonical && ./run.sh
```

-----------
Alternative:

1. (Optional) Create a Python 3 virtual environment.
```bash
$ pip3 install virtualenv
$ virtualenv -p python3 venv
$ source venv/bin/activate
```
2. Install the requirements.
```bash
(venv)$ pip install -r requirements.txt
```
3. Run Cell Universe on an example video (look inside the `run.sh` script to see the command used).
```bash
(venv)$ cd examples/canonical && ./run.sh
```

Steps to get a new video running:
---------------------------------
1. Separate your video into still images, one image per frame and put them in a directory with chronological names like `frame000.png`, `frame001.png`, etc.
2. Cell Universe needs a starting "guess" for the number, location, size, and orientation of the cells in the first frame. You can do this visually using the cell labeling tool Python program. This will create an initial properties file with all that information. See the `tools/cell_labeling_tool/README.md` for more information about the cell labeling tool and `examples/canonical/initial.csv` for an example initial properties file generated by the cell labeling tool.
```bash
(venv)$ python3 tools/cell_labeling_tool/cell_labeling_tool.py
```
3. Create a Cell Universe config file and fill it in with settings that fits your cell video. See `examples/canonical/global_optimizer_config.json` (and the comments in the file) and other config JSON files in the `examples` directory for examples.
    * It is important to change settings in the config file like the "Global Setttings" and the "Bacilli Settings" to match your cell video, or else Cell Universe will not function properly.
4. Run Cell Universe, giving it the input directory containing the frames, the initial properties file created above, the config file, as well as the other required arguments (see in the Usage section).
    * An example command is at the bottom of this README and more in the `run.sh` script files in the `examples` directory.

There are several examples in subdirectories below the directory `regression-tests` and `examples`.

Usage
-----

Command line help:

``` sourceCode
usage: main.py [-h] [-d DIRECTORY] [-ff N] [-lf N] [--dist] [-w WORKERS] [-j JOBS] [--keep KEEP]
               [--strategy STRATEGY] [--cluster CLUSTER] [--no_parallel] [--global_optimization]
               [--binary] [--graySynthetic] [--phaseContrast] [-ta TEMP] [-ts START_TEMP]
               [-te END_TEMP] [-am {none,frame,factor,const,cost}] [-r FILE] [--lineage_file FILE]
               [--continue_from N] [--seed N] [--batches N] -i PATTERN -o DIRECTORY -c FILE -x FILE -b FILE
               
optional arguments:
  -h, --help            show this help message and exit
  -d DIRECTORY, --debug DIRECTORY
                        path to the debug directory (enables debug mode)
  -ff N, --frame_first N
                        starting image (default: 0)
  -lf N, --frame_last N
                        final image (defaults to until last image)
  --dist                use distance-based objective function
  -w WORKERS, --workers WORKERS
                        number of parallel workers (defaults to number of
                        processors)
  -j JOBS, --jobs JOBS  number of jobs per frame (defaults to --workers/-w)
  --keep KEEP           number of top solutions kept (must be equal or less
                        than --jobs/-j)
  --strategy STRATEGY   one of "best-wins", "worst-wins", "extreme-wins"
  --cluster CLUSTER     dask cluster address (defaults to local cluster)
  --no_parallel         disable parallelism
  --global_optimization
                        global optimization
  --binary              input image is binary
  --graySynthetic       enables the use of the grayscale synthetic image for
                        use with non-thresholded images
  --phaseContrast       enables the use of the grayscale synthetic image for
                        phase contract images
  -ta TEMP, --auto_temp TEMP
                        auto temperature scheduling for the simulated
                        annealing
  -ts START_TEMP, --start_temp START_TEMP
                        starting temperature for the simulated annealing
  -te END_TEMP, --end_temp END_TEMP
                        ending temperature for the simulated annealing
  -am {none,frame,factor,const,cost}, --auto_meth {none,frame,factor,const,cost}
                        method for auto-temperature scheduling
  -r FILE, --residual FILE
                        path to the residual image output directory
  --lineage_file FILE   path to previous lineage file
  --continue_from N     load already found orientation of cells and start from
                        the continue_from frame
  --seed N              seed for random number generation
  --batches N           number of batches to split each frame into for
                        multithreading

required arguments:
  -i PATTERN, --input PATTERN
                        input filename pattern (e.g. "image%03d.png")
  -o DIRECTORY, --output DIRECTORY
                        path to the output directory
  -c FILE, --config FILE
                        path to the configuration file
  -x FILE, --initial FILE
                        path to the initial cell configuration
  -b FILE, --bestfit FILE
                        path to the best fit synthetic image output directory
```

Examples
--------

``` sourceCode
python3 src/main.py --frame_first 0 --frame_last 13 --input "./examples/canonical/input/gray/frame%03d.png" \
  --output "./examples/canonical/output" --bestfit "./examples/canonical/output/bestfit" --config \
  "./examples/canonical/global_optimizer_config.json" --initial "./examples/canonical/initial.csv" --no_parallel \
  --graySynthetic --global_optimization
```
