==========================================================
Running a jupyter-notebook on Ozstar or Ngarrgu Tindebeek
==========================================================

Step 1:
"""""""
ssh into remote host (ozstar):

.. code:: bash

  ssh myusername@nt.swin.edu.au

Check whether jupyter-notebook exists by running:

.. code:: bash

  which jupyter-notebook

If the command returns something like:

*no jupyter-notebook in ...*

then you have to add these modules:

.. code:: bash
  
  module load gcc/12.3.0
  module load ipython/8.14.0

to your .bashrc file then do "source ~/.bashrc". The versions (12.3.0 and 8.14.0) might be differetn at the time you read.


Step 2:
"""""""
Request an interactive node with some resources using the srun wrapper, sinteractive:

.. code:: bash

  sinteractive --time=1:00:00 --mem=2GB

This will request 1 cpu with 2 GB of RAM with a walltime of 1 hour for your jupyter-notebook session.

When the resources are allocated, you will be returned a command prompt on the compute node that you've been granted. Note the name of the compute node by running:

.. code:: bash
  
  echo $HOSTNAME

This should return something like: dave41


Step 3:
"""""""

Start a jupyter-notebook server but without a browser by running the following command:

.. code:: bash

  jupyter-notebook --no-browser --ip=$HOSTNAME

The "--ip=$HOST" flag specifies that the notebook server will listen on daveXX IP address rather than localhost.

If the server successfully launches, it should print something like this:

*To access the notebook, open this file in a browser:*
    *file:///home/vdimarco/.local/share/jupyter/runtime/nbserver-3570438-open.html*
*Or copy and paste one of these URLs:*
    *http://dave41:8888/?token=68b218d3bf779bcc286383a66b7a3884075ed322a17d97de*
*or http://127.0.0.1:8888/?token=68b218d3bf779bcc286383a66b7a3884075ed322a17d97de*

Note the port number that the server binds to (8888 in this case), let's call it the *remote_port*, as we're going to use it in the next step. You can also specify a different port by using the "--port" option.

Step 4:
"""""""

In a new terminal, ssh into ozstar, but this time using the port forwarding flag:

.. code:: bash

  ssh myusername@nt.swin.edu.au -L local_port:host:remote_port

replacing *remote_port* by what was given in step 3 (8888 in this example), and *host* by what was given in step 2 (dave41 in this example). For simplicity, let's make *local_port=remote_port*.
So in our example it will be "ssh myusername@nt.swin.edu.au -L 8888:dave41:8888"

Finally, launch your favorite browser, copy/paste the address that the jupyter-notebook is running at, as given in step 3, and replace the host name in the address ("dave41" in this case) by "localhost". In case *local_port!=remote_port*, replace the *remote_port* number in the address by the *local_port* that you've selected.
So, in our example it will be "http://localhost:8888/?token=68b218d3bf779bcc286383a66b7a3884075ed322a17d97de"
