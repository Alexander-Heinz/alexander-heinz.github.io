---
title: "Managing Projects And Requirements In Anaconda"
date: 2021-04-04
tags: [anaconda, conda, workflow, projects, managing, requirements, requirements.txt]
header:
  image: "/images/managing-projects/anaconda.jpg"
excerpt: "How to create a requirements.txt and install it via a virtualenv in conda"
mathjax: "true"
---


### Managing projects in anaconda: The Workflow

Often, it is important for certain projects and python files that a certain version of a certain module is installed, or even a certain python version. 

You might also have cloned a project via git that has no `requirements.txt` and you start getting tired of installing each and every module manually.

But don't worry: Here, I will list the steps in order to manage projects in anaconda in a clean, safe way.
You will learn how to:
- auto-generate and save `requirements.txt` for a given file / project
- create a virtual environment in `conda`
(A virtual environment is an isolated workspace where you install your packages separate from the main system installation)
- install all required packages in the `requirements.txt` in that environment
That way, we can avoid unnecessary conflicts between versions of packages and python versions. 

# Using pipreqs to save the requirements.txt of a project or file
### 1. Install pipreqs
In your terminal, type `pip install pipreqs` to install pipreqs

### 2. Save the required modules as requirements.txt of a project
- Using the pipreqs command `pipreqs /path/to/project`

Or:
- navigate to the folder of the project in the terminal
- type `pipreqs .` to include all packages

Example:
<img src="{{ site.url }}{{ site.baseurl }}/images/managing-projects/pipreqs.jpg" alt="pipreqs-pic">


Your `requirements.txt` will be created

# Create a virutal environment in anaconda and install requirements.txt
### 1. Create a virtual environment for your project in conda
* To create a new environment without further specifications:
    type `conda create --name myenv`

* To create an environment with a specific version of Python:
    Type `conda create -n myenv python=3.9.2` (where 3.9.2 is your desired version)

where `myenv` is your environment name

* don't forget to activate your environment using `conda activate myenv`

<img src="{{ site.url }}{{ site.baseurl }}/images/managing-projects/conda-create.jpg" alt="pic">


See [this link](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) for more information about managing environments in conda.

### 2. Install the requirements in that new environment

* Just use `pip install -r requirements.txt` to install all the required packages in their versions!