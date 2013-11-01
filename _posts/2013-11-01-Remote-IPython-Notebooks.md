---
layout: post
title: "Remote IPython Notebooks"
description: ""
tagline: "Accessing IPython in a browser"
category: articles
tags: [article, Python, IPython, howto]
---
{% include JB/setup %}

## Task

We’d like to run an [IPython notebook](http://ipython.org/notebook.html) session on a remote computer.

## Howto

<p><span class="label label-important">Important</span> We should always secure IPython notebook. Otherwise an attacker is able to run arbitrary commands on your system.</p>

To increase the security, we’ll follow the [IPython documentation](http://ipython.org/ipython-doc/rel-1.1.0/interactive/public_server.html#notebook-security) and use both a password and ssl encryption.

Let’s first create a password. Just enter the password when asked for it and note the resulting output:

    $ ipython
    Python 2.7.3

    In [1]: from IPython.lib import passwd

    In [2]: passwd()
    Enter password: 
    Verify password: 
    Out[2]: 'sha1:58f7733bc161:e8d00c4cfc7e4c535082931323ff32087b30b816'


In order to avoid someone sniffing (or injecting) our data, we’ll also use a self-signed ssl certificate which is used to encrypt our data.

    $ openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

This creates a file `mycert.pem` in the current folder which contains both key and certificate data.

Finally, we need to configure IPython notebook to use this data. The best approach will be to keep a separate profile for these settings. Let’s create the profile:

    $ ipython profile create nbserver

IPython will then tell you that it has created two config files. `ipython_config.py` and `ipython_notebook_config.py`. Open `ipython_notebook_config.py` in an editor and add the following (or uncomment and change the default variables).

    c = get_config()
    
    # Kernel config
    c.IPKernelApp.pylab = 'inline' # enables inline graphics
    
    # Notebook config
    c.NotebookApp.certfile = u'/absolute/path/to/your/certificate/mycert.pem'
    c.NotebookApp.ip = '*' # allows access from everywhere
    c.NotebookApp.open_browser = False
    c.NotebookApp.password = u'sha1:58f7733...[your hashed password here]'
    # It is a good idea to put it on a known, fixed port
    c.NotebookApp.port = 9999

Additionally, we have specified that we want to use inline graphics for all plots.

We can now start the secured IPython notebook by executing

    $ ipython notebook --profile=nbserver
    [NotebookApp] Using existing profile dir: u'~/.config/ipython/profile_nbserver'
    [NotebookApp] Serving notebooks from nbserver
    [NotebookApp] The IPython Notebook is running at: http://*:9999/
    [NotebookApp] Use Control-C to stop this server and shut down all kernels.

and access the notebook in the browser using the URL *http://[your-computer]:9999/*. (You may receive a warning concerning your self-signed certificate but that’s okay.)

(Of course, you should always use Control-C when finished.)

Just as a normale IPython session, the IPython notebook will run from the directory it was started from. Therefore one may access all files in there and also run them (using the `%run file.py` directive, for example).


