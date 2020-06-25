---
title: Python Virtual Environment
date: 2020-06-25 15:00:00 -0500
categories: [Tech Blog, Operation Demo]
tags: [python virtual environment]
---

## To create a virtual environment

Refer to: [How to create a virtual environment using Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

To create a virtual env with name 'myenv':
```
conda create --name myenv
```

To create a virtual env with environment.yml file:
```
conda env create -f environment.yml
```

The environment.yml specifies the dependencies and it looks like this:
```
name: myenv

dependencies:
    - python=3.5
    - pandas
    - numpy
    - matplotlib
    - pip:
        - tensorflow==1.10
```

If not assigned a directory, the virtual env can be found in somewhere like this:
```
C:\Users\your-user-name\Anaconda3\envs
```

## To activate, deactivate, and delete a virtual env
```
conda activate myenv
conda deactivate
conda remove --name myenv --all
```


## Set the kernel of Spyder to be the virtual env

Refer to: [How to use Spyder in different virtual environment](https://stackoverflow.com/questions/30170468/how-to-run-spyder-in-virtual-environment).

If we want our codes in Spyder to be compiled in the virtual env, we need to install a new Spyder in the virtual env.

1. Activate the virtual env:
```
conda activate myenv
```

2. Install Spyder:
```
conda install spyder
```

3. Finally, just open the spyder and it will be running inside the virtual env:
```
spyder
```

## Set the kernel of Jupyter Notebook to be the virtual env

Refer to: [How to use Jupyter Notebook in different virtual environment](https://janakiev.com/blog/jupyter-virtual-envs/).

1. Activate the virtual env:
```
conda activate myenv
```

2. Install kernel for this virtual env:
```
pip install --user ipykernel
```

3. Configure this kernel with Jupyter Notebook
```
python -m ipykernel install --user --name=myenv
```

4. All set. 

    If you haven't create a Jupyter Notebook file, just open Jupyter Notebook and create one and specify the env while creating.
    ```
    jupyter notebook
    ```
    ![](/assets/img/tech-blog/notes/virtualenv/select_env.png)

    Else if you already have one, you can just configure it to run in the virtual env:

    ![](/assets/img/tech-blog/notes/virtualenv/select_env2.png)

5. To delete.

    The virtual env kernel file can be found in somewhere:
    ```
    C:\Users\your-user-name\AppData\Roaming\jupyter\kernels
    ```

    Or use cmd line:
    See what kernels are there:
    ```
    jupyter kernelspec list
    ```

    Delete what you desire, e.g. myenv.
    ```
    jupyter kernelspec uninstall myenv
    ```

## Set the kernel of VS Code to be the virtual env

Refer to: [How to use VS Code in different virtual environment](https://code.visualstudio.com/docs/python/environments).

Note: If you have Annaconda installed, the VS Code should recognize all the virtual envs your created with Annaconda.

## About Docker

Like the native virtual env, Docker is also capable of handling package version issues, but comes with less space requirement. Future introduction will be given to it. A few useful references are listed here:
- [https://www.youtube.com/watch?v=SR95WmOSm0c](https://www.youtube.com/watch?v=SR95WmOSm0c)
- [https://runnable.com/docker/python/dockerize-your-python-application](https://runnable.com/docker/python/dockerize-your-python-application)
- [https://towardsdatascience.com/docker-for-python-development-83ae714468ac](https://towardsdatascience.com/docker-for-python-development-83ae714468ac)


