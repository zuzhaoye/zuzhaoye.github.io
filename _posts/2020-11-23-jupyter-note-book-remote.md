---
title: Jupyter Notebook Remote Server Setup
date: 2020-11-23 15:00:00 -0500
categories: [Tech Blog, Operation Demo]
tags: [jupyter notebook]
---
Reference Video:[Youtube](https://www.youtube.com/watch?v=qeJUsahqzw8)

## From the server
```
jupyter notebook --ip 0.0.0.0 --port 8889
```
Result:
![](/assets/img/tech-blog/notes/jupyter/set-up.png)


## From the remote
Enter the address to a web browser.
```
http://technhit-lab:8889/
```
Result:
![](/assets/img/tech-blog/notes/jupyter/enter-token.png)


## Enter the credentials
You can either copy the token (show in the 1st figure) or set up a password for Jupyter Notebook. To set up a password, read the following.


## Set up a password for Jupyter
From the server side:
```
jupyter notebook password
```
Then set the password accordingly.


## Delete a password
For linux system, go to
```
/home/USERNAME/.jupyter/jupyter_notebook_config.py
```
Then change the password to
```
password = ""
```


