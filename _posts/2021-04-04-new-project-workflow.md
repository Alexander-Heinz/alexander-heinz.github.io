
#### Managing projects in anaconda: Workflow

To get into the `conda` workflow, I will here list the steps in order to 
- auto-generate and save `requirements.txt` for a given file / project
- create a virtual environment in `conda`
- install all required packages in the `requirements.txt` in that environment
A virtual environment is an isolated workspace where you install your packages separate from the main system installation.
That way, we can avoid unnecessary conflicts between versions of packages and python versions. 


### Install pipreqs
In your terminal, type `pip install pipreqs`

### save the requirements.txt of a project
- Using the pipreqs command `pipreqs /path/to/project`

Or:
- navigate to the folder of the project in the terminal
- type `pipreqs .` to include all packages


### Create a virtual environment for your project
*where `myenv` is your environment name*
* To create a new environment without further specifications:
    type `conda create --name myenv`

* To create an environment with a specific version of Python:
    Type `conda create -n myenv python=3.9.2` (where 3.9.2 is your desired version)

*don't forget to activate your environment using `conda activate myenv`*

See [this link](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) for more information about managing environments in conda.

### Install the requirements in that new environment

* Just use `pip install -r requirements.txt` to install all the required packages in their versions according to the `requirements.txt` file!