Local Network Testing
=====================
NCTL stands for network/node control. `NCTL <https://github.com/casper-network/casper-node/tree/master/utils/nctl>`_ is a CLI application you can use to set up and control multiple local Casper networks during development. Many developers wish to spin up relatively small test networks to localize their testing before deploying to the blockchain. Adopting a standardized approach in the community is also helpful for troubleshooting and reporting issues.

Prerequisites 
^^^^^^^^^^^^^

#. You have completed the `Getting Started section <dapp-dev-guide/setup-of-rust-contract-sdk>`_, which shows you how to install your development environment, including tools like *CMake* (version 3.1.4+), *Cargo*, and *Rust*.
#. Make sure you have `Python 3 installed <https://www.python.org/downloads/>`_ if your operating system does not include Python.
#. To run NCTL, you will also need `the bash shell <https://www.gnu.org/software/bash/>`_.

Video Tutorial
^^^^^^^^^^^^^^

If you prefer a video walkthrough of this guide, you can check out this video.

.. raw:: html 

   <iframe width="560" height="315" src="https://www.youtube.com/embed?v=Vg3tr5iScPY&list=PL8oWxbJ-csErqfzYvbWsMUr4IvwRVenni&index=2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Installing a Virtual Environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
We will show you how to run NCTL in a virtual environment. If you want to run NCTL at the system level, you can, but we recommend that you only do that if you are sure you know what you are doing.

First, you will need to install a set of tools required for running NCTL.
 
**Step 1.** The first tool you will need is **pip**, a package manager for Python. Pip comes with the Python 3 installation from python.org, but if you do not have it already, follow the steps below or `the full installation instructions <https://pip.pypa.io/en/stable/installing/>`_.

Instructions for MacOS:

.. code-block:: bash

   $ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
   $ python3 get-pip.py

Instructions for Linux:

.. code-block:: bash

	$ sudo apt install python3-pip

**Step 2.** Install **pkg-config**, a program used to compile and link against one or more libraries.

Instructions for MacOS:

.. code-block:: bash

	$ brew install pkg-config

Instructions for Linux:

.. code-block:: bash

	$ sudo apt install pkg-config

**Step 3.** Install either **libssl-dev** (Linux) or **openssl** (MacOS), which are toolkits for the Transport Layer Security (TLS) and Secure Sockets Layer (SSL) protocols. They also serve as general-purpose cryptography libraries.

Instructions for MacOS:

.. code-block:: bash

	$ brew install openssl

Instructions for Linux:

.. code-block:: bash

	$ sudo apt install libssl-dev

**Step 4.** You will also need the **gcc** and **g++** compilers, which usually come as part of developer command-line tools (versions 7.5.0 at the time of this writing).

Instructions for MacOS:

.. code-block:: bash

   $ xcode-select --install
   $ gcc --version
   $ g++ --version

Instructions for Linux:

.. code-block:: bash

   $ sudo apt install build-essential
   $ gcc --version
   $ g++ --version

**Step 5.** Create and activate a new virtual environment. **Commands applicable to the virtual environment will be prefixed with (env)**. Run the following commands to set it up.

Instructions for MacOS and Linux:

.. code-block:: bash

   $ python3 -m venv env
   $ source env/bin/activate
   (env) $

**Step 6.** Inside the virtual environment, upgrade **pip** to the latest version.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ pip install --upgrade pip

**Step 7.** Install **jq**, a command-line JSON processor.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ pip install jq

**Step 8.** Install **supervisor**, a cross-platform process manager.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ pip install supervisor

**Step 9.** Install **toml**, a configuration file parser.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ pip install toml


Setting up the Network
^^^^^^^^^^^^^^^^^^^^^^^
You are now ready to set up and run your local network of Casper nodes.
 
**Step 10.** Clone the *casper-node-launcher* software in your working directory, which we will call *WORKING_DIRECTORY*. **Very Important!!! Choose a short path for your working directory**; otherwise, the NCTL tool will report that the path is too long.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ cd <WORKING_DIRECTORY>
   (env) $ git clone https://github.com/casper-network/casper-node-launcher
 
**Step 11.** Next, clone the *casper-node* software, also in your working directory.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ git clone https://github.com/casper-network/casper-node
 
**Step 12.** Activate the NCTL environment with the following command.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ source casper-node/utils/nctl/activate

**Step 13.** Compile the NCTL binary scripts. The following command compiles both the *casper-node* and the *casper-client* in release mode.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ nctl-compile
 
**Step 14.** Set up all the assets required to run a local network, including binaries, chainspec, config, faucet, and keys. Also, spin up the network right after. The default network will have 10 nodes, with 5 active nodes and 5 inactive nodes.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ nctl-assets-setup && nctl-start

Once a network is up and running, you can control each node within the network and add new nodes to the network.
 
Several other NCTL commands are available via aliases for execution from within a terminal session. All such commands are prefixed by *nctl-* and allow you to perform various tasks.

You should see the new directory *utils/nctl/assets*, with the following structure.

.. image:: ../assets/nctl/assets_setup.png
  :width: 200
  :alt: Image showing the folders created by nctl.

| 

Here is the command line output you would expect.

.. image:: ../assets/nctl/nctl_output.png
  :alt: Image showing successful nctl output.

| 

Stopping the Network
^^^^^^^^^^^^^^^^^^^^
**Step 15.** Although not necessary, you can stop and clean the NCTL setup with the following commands.

Instructions for MacOS and Linux:

.. code-block:: bash

   (env) $ nctl-stop
   (env) $ nctl-clean
 
Next Steps
^^^^^^^^^^
#. Explore the `various NCTL commands <https://github.com/casper-network/casper-node/blob/master/utils/nctl/docs/commands.md>`_.
#. Explore the `NCTL usage guide <https://github.com/casper-network/casper-node/blob/master/utils/nctl/docs/usage.md>`_.

