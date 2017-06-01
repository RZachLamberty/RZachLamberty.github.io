---
layout: post
title: Jupyterhubris
excerpt: "In which our hero bestows Jupyter upon us all"
keywords: "hack, aws, linux, ubuntu, jupyter, jupyterhub, configuration, conda, permissions, admin, sysad"
series: aws
part: 2
---

## installing `jupyterhub` on aws

*the following is based entirely on
[this page](https://jupyterhub.readthedocs.io/en/latest/quickstart.html)*

### prerequisites

first, create a regular ol' linux environment. What I did was use the free tier
eligible publicly available ubuntu 16 LTS ami. other features:

+ open ports 22, 80, and 443 to your own ip address or the world
+ get that pem key bwoi

At first I installed miniconda, but making that play with sudo felt weird to
me. So if you still want to do that, head on
over [here](https://conda.io/miniconda.html)


```shell
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.shaper
bash Miniconda3-latest-Linux-x86_64.shaper
```

otherwise, you can use `pip`

``` shell
sudo apt-get install python3-pip
pip3 install --upgrade pip
```

next, had to update apt cache and install `npm` and `nodejs-legacy`

```shell
sudo apt-get update
sudo apt-get install npm nodejs-legacy
```

now, time to actually install jupyterhub. Note: if you want to have multiple
users, you *may* have to run the following as `sudo`. I haven't dug into it
enough yet to know if that's true. Obviously, `sudo pip install`-ing is frowned
upon, but do what you want bruh.

``` shell
python3 -m pip install jupyterhub
python3 -m pip install notebook
```

### runnin shit

first, you have to have users you can log in as (with passwords). fortunately,
aws already locked things down for the most part, so we don't need to be crazy
paranoid about giving a user password (though maybe that's debatable, I don't
know). I know that `/etc/ssh/sshd_config` has password authentication disabled
by default, so even if we create a user for everyone at ERI and give them each a
password, they'll still have to `ssh` in as desired.

anyway, the default authentication module for `jupyterhub` is PAM, and it uses
the ubuntu machine's authentication. So create a user and set their password

``` shell
adduser --ingroup jupyterhub WHATEVER_USERNAME_YOU_WANT
# give them whatever password you want; they're stuck with that shit
```

``` shell
sudo su -
jupyterhub --port 80
```

now try connecting to that from a distance with the username and password you
picked in the `adduser` stage.


### some auth shit

I decided to update the configuration file in a few ways

first, I created a basic config file:

``` shell
jupyterhub --generate-config
```

this created a file called `jupterhub_config.py` in my current directory. After
this, I updated the default port to be 80:

``` python
## The public facing port of the proxy
c.JupyterHub.port = 80
```

next, I created a secret cookie file in the recommended location

``` shell
mkdir /srv/jupyterhub
openssl rand -base64 2048 > /srv/jupyterhub/cookie_secret
chmod 600 /srv/jupyterhub/cookie_secret
```

and added the path to this cookie file to my `jupterhub_config.py` file:

``` python
## File in which to store the cookie secret.
c.JupyterHub.cookie_secret_file = '/srv/jupyterhub/cookie_secret'
```

I also went ahead and created a proxy authentication token (again, just because,
no really knowledge of what this is or how it matters). The token is generated
via

``` shell
openssl rand -hex 32
```

and then added to the `jupyterhub_config.py` file:

``` python
#  Loaded from the CONFIGPROXY_AUTH_TOKEN env variable by default.
c.JupyterHub.proxy_auth_token = 'CONTENTS_OF_THE_ABOVE'
```

the config file supports a whitelist of user names as parameter
`c.Authenticator.whitelist`. this value expects a python set.

also! admin users, also as a python set of user names. these users still have to
viable under the PAM authentication, but if they are, they will have access to
an admin page within the jupyterhub notebooks when they log in.


### users and conda environments

so I haven't figured out the best way to do this yet. I think the best option
here is to have users install miniconda *themselves*, and then use the following
commands (from an open terminal via ssh or jupyter) to create a *kernel* that can
be used in the future and is pointed at that conda environment

``` shell
# let's assume we have installed conda or miniconda into CONDA_DIR
# (e.g. ~/miniconda3)
conda install ipython ipykernel
ipython kernel install --user --name conda

# and if we have any environments CONDA_ENV we want to install:
source activate $CONDA_ENV
conda install ipython ipykernel
ipython kernel install --user --name $CONDA_ENV
```

hat tip to [this walkthrough](https://zonca.github.io/2015/10/use-own-python-in-jupyterhub.html)
