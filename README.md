![SimOpt Logo](.github/resources/logo_full_magnifying_glass.png)

## About the Project
SimOpt is a testbed of simulation-optimization problems and solvers. Its purpose is to encourage the development and constructive comparison of simulation-optimization (SO) solvers (algorithms). We are particularly interested in the finite-time performance of solvers, rather than the asymptotic results that one often finds in related literature.

For the purposes of this project, we define simulation as a very general technique for estimating statistical measures of complex systems. A system is modeled as if the probability distributions of the underlying random variables were known. Realizations of these random variables are then drawn randomly from these distributions. Each replication gives one observation of the system response, i.e., an evaluation of the objective function or stochastic constraints. By simulating a system in this fashion for multiple replications and aggregating the responses, one can compute statistics and use them for evaluation and design. 

This respository is specifically designed to allow for the reproduction of figures from Felice et. al. (2025) Diagnostic Tools for Evaluating Solvers for Stochastically Constrained Simulation Optimization Problems therefore many of SimOpt's capabilities have been limited. The full unlimited version of SimOpt can be found here: https://github.com/simopt-admin/simopt 

Several papers have discussed the development of SimOpt and experiments run on the testbed:
* [Eckman et al. (2024)](https://ieeexplore.ieee.org/document/10408734) studies feasibility metrics for stochastically constrained simulation-optimization problems in preparation for introducing related metrics in SimOpt.
* [Shashaani et al. (2024)](https://dl.acm.org/doi/10.1145/3680282) conducts a large data-farming experiment over solver factors to learn relationships between their settings and a solver's finite-time performance.
* [Eckman et al. (2023)](https://pubsonline.informs.org/doi/10.1287/ijoc.2023.1273) is the most up-to-date publication about SimOpt and describes the code architecture and how users can interact with the library.
* [Eckman et al. (2023)](https://pubsonline.informs.org/doi/10.1287/ijoc.2022.1261) introduces the design of experiments for comparing solvers; this design has been implemented in the latest Python version of SimOpt. For detailed description of the terminology used in the library, e.g., factors, macroreplications, post-processing, solvability plots, etc., see this paper.
* [Eckman et al. (2019)](https://www.informs-sim.org/wsc19papers/374.pdf) describes in detail changes to the architecture of the MATLAB version of SimOpt and the control of random number streams.
* [Dong et al. (2017)](https://www.informs-sim.org/wsc17papers/includes/files/179.pdf) conducts an experimental comparison of several solvers in SimOpt and analyzes their relative performance.
* [Pasupathy and Henderson (2011)](https://www.informs-sim.org/wsc11papers/363.pdf) describes an earlier interface for MATLAB implementations of problems and solvers.
* [Pasupathy and Henderson (2006)](https://www.informs-sim.org/wsc06papers/028.pdf) explains the original motivation for the testbed.

## Code
### Python
- The [`master branch`](https://github.com/simopt-admin/simopt/tree/master) contains the source code for the latest stable release of the testbed
- The [`development branch`](https://github.com/simopt-admin/simopt/tree/development) contains the latest code for the testbed, but may contain more bugs than the master branch


## Getting Started
### Requirements
- Python >= 3.8
    - To check your Python version, open a terminal and run `python --version`. If you see a message along the lines of `Command not found`, then you likely don't have Python installed. If you know you have it installed but are getting a `Command not found` error, then you may need to [add Python to your PATH](https://realpython.com/add-python-to-path/).
    - For new installs, [Miniconda or Anaconda](https://www.anaconda.com/download) is recommended ([read about the differences between Miniconda and Anaconda](https://docs.anaconda.com/distro-or-miniconda/)). If you already have a compatible IDE (such as VS Code), we've found that Miniconda will work fine at 1/10 of the size of Anaconda. It is ***highly recommended*** to check the box during installation to add Python/Miniconda/Anaconda to your system PATH.
- Ruby >= 2.5 (required for datafarming)
    - Included on MacOS, but Windows users will need to grab it from [here](https://rubyinstaller.org/).
- `datafarming` gem < 2.0 (required for datafarming)
    - This can be installed via `gem install datafarming -v 1.4` once Ruby is installed/configured.
    - If experiencing issues, make sure that you are not using version 2.0 or later as those releases does not include the required files.
- Python packages for `numpy`, `scipy`, `matplotlib`, `pandas`, `seaborn`, `sphinx`, `mrg32k3a`, `jupyter`, and 'cvxpy'.
    -  run `pip install numpy scipy matplotlib pandas seaborn sphinx mrg32k3a jupyter cvxpy`.

### Downloading Source Code
There are two ways to download a copy of the source code onto your machine:
1. Download the code in a zip file by clicking the green `<> Code` button above repo contents and clicking the `Download ZIP` option, then unzip the code to a folder on your computer. This does not require `git` to be installed but makes downloading updates to the repository more challenging.
![image](https://github.com/user-attachments/assets/3c45804c-f8b0-48ed-b32c-a443550c6ef5)

3. [Clone](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository) the branch you'd like to download to a folder on your computer. This requires `git` to be installed but makes downloading updates to the repository much easier.

If you do not need the source code for SimOpt, you may install the library as a Python package instead. See the [Package](#package) and [Basic Example](#basic-example) sections for more details about this option.

## Package
The `simoptlib` package is available to download through the Python Packaging Index (PyPI) and can be installed from the terminal with the following command:
```
python -m pip install simoptlib
```

## Basic Example
After installing `simoptlib`, the package's main modules can be imported from the Python console (or in code):
```
import simopt
from simopt import models, solvers, experiment_base
```

The following snippet of code will run 10 macroreplications of the Random Search solver ("RNDSRCH") on the Continuous Newsvendor problem ("CNTNEWS-1"):
```
myexperiment = simopt.experiment_base.ProblemSolver("RNDSRCH", "CNTNEWS-1")
myexperiment.run(n_macroreps=10)
```

The results will be saved to a .pickle file in a folder called `experiments/outputs`. To post-process the results, by taking, for example 200 postreplications at each recommended solution, run the following:
```
myexperiment.post_replicate(n_postreps=200)
simopt.experiment_base.post_normalize([myexperiment], n_postreps_init_opt=200)
```

A .txt file summarizing the progress of the solver on each macroreplication can be produced:
```    
myexperiment.log_experiment_results()
```

A .txt file called `RNDSRCH_on_CNTNEWS-1_experiment_results.txt` will be saved in a folder called `experiments/logs`.

One can then plot the mean progress curve of the solver (with confidence intervals) with the objective function values shown on the y-axis:
```
simopt.experiment_base.plot_progress_curves(experiments=[myexperiment], plot_type="mean", normalize=False)
```

## Diagnostic Tools Demo

To reproduce the figures found in the diagnostic tools paper, run the file diagnostic_tools_demo.ipynb using Jutpyter Notebook. 

## Authors
The core development team currently consists of 
- [**David Eckman**](https://eckman.engr.tamu.edu) (Texas A&M University)
- [**Sara Shashaani**](https://shashaani.wordpress.ncsu.edu) (North Carolina State University)
- [**Shane Henderson**](https://people.orie.cornell.edu/shane/) (Cornell University)
- [**William Grochocinski**](https://github.com/Grochocinski) (North Carolina State University)

## Citation
To cite this work, please use
```
@misc{simoptgithub,
  author = {D. J. Eckman and S. G. Henderson and S. Shashaani and R. Pasupathy},
  title = {{SimOpt}},
  year = {2024},
  publisher = {GitHub},
  journal = {GitHub repository},
  howpublished = {\url{https://github.com/simopt-admin/simopt}},
  commit = {21802a685ec394fed048820e692209628f40dd4e}
}
```

## Acknowledgments
An earlier website for SimOpt ([http://www.simopt.org](http://www.simopt.org)) was developed through work supported by the following grants:
- National Science Foundation
    - [DMI-0400287](https://www.nsf.gov/awardsearch/showAward?AWD_ID=0400287)
    - [CMMI-0800688](https://www.nsf.gov/awardsearch/showAward?AWD_ID=0800688)
    - [CMMI-1200315](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1200315)

Recent work on the development of SimOpt has been supported by the following grants
- National Science Foundation
    - [IIS-1247696](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1247696)
    - [CMMI-1254298](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1254298)
    - [CMMI-1536895](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1536895)
    - [CMMI-1537394](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1537394)
    - [DGE-1650441](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1650441)
    - [DMS-1839346](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1839346) (TRIPODS+X)
    - [CMMI-2206972](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2206972)
    - [OAC-2410948](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2410948)
    - [OAC-2410949](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2410949)
    - [OAC-2410950](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2410950)
- Air Force Office of Scientific Research
    - FA9550-12-1-0200
    - FA9550-15-1-0038
    - FA9550-16-1-0046
- Army Research Office
    - W911NF-17-1-0094

*Any opinions, findings and conclusions or recommendations expressed in this material are those of the authors and do not necessarily reflect the views of the National Science Foundation (NSF).*
