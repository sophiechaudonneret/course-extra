# How to install PyTorch on the Duckiebot {#pytorch-install status=ready}

PyTorch is a Python deep learning library that's currently gaining a lot of traction, because it's a lot easier to debug and prototype (compared to TensorFlow / Theano).

To install PyTorch on the Duckietbot you have to compile it from source, because there is no pro-compiled binary for ARMv7 / ARMhf available. This guide will walk you through the required steps.

## Step 1: install dependencies and clone repository

First you need to install some additional packages. You might already have installed. If you do, that's not a problem.

    sudo apt-get install libopenblas-dev cython libatlas-dev m4 libblas-dev

In your current shell add two flags for the compiler

    export NO_CUDA=1 # this will disable CUDA components of PyTorch, because the little RaspberriPi doesn't have a GPU that supports CUDA
    export NO_DISTRIBUTED=1 # for distributed computing

Then `cd` into a directory of your choice, like `cd ~/Downloads` or something like that and clone the PyTorch library.

    git clone --recursive https://github.com/pytorch/pytorch

## Step 2: Change swap size

When I was compiling the library I ran out of SWAP space (which is 500MB by default). I was successful in compiling it with 2GB of SWAP space. Here is how you can increase the SWAP (only for compilation - later we will switch back to 500MB).

Create the swap file of 2GB

    sudo dd if=/dev/zero of=/swap1 bs=1M count=2048

Make this empty file into a swap-compatible file

    sudo mkswap /swap1

Then disable the old swap space and enable the new one

    sudo nano /etc/fstab

This above command will open a text editor on your `/etc/fstab` file. The file should have this as the last line: `/swap0 swap swap`. In this line, please change the `/swap0` to `/swap1`. Then save the file with <kbd>CTRL</kbd>+<kbd>o</kbd> and <kbd>ENTER</kbd>. Close the editor with <kbd>CTRL</kbd>+<kbd>x</kbd>.

Now your system knows about the new swap space, and it will change it upon reboot, but if you want to use it right now, without reboot, you can manually turn off and empty the old swap space and enable the new one:

    sudo swapoff /swap0
    sudo swapon /swap1

## Step 3: compile PyTorch

`cd` into the main directory, that you clones PyTorch into, in my case `cd ~/Downloads/pytorch` and start the compilation process:

    python setup.py build

This shouldn't create any errors but it took me about an hour. If it does throw some exceptions, please let me know.

When it's done, you can install the pytorch package system-wide with

    sudo -E python setup.py install # the -E is important

For some reason on my machine this caused recompilation of a few packages. So this might again take some time (but should be significantly less).

## Step 4: try it out

If all of the above went through without any issues, congratulations. :) You should now have a working PyTorch installation. You can try it out like this.

First you need to change out of the installation directory (**this is important - otherwise you get a really weird error**):

    cd ~

Then run Python:

    python

And in the Python interpreter try this:

<pre>
<code>&#8203;>&#8203;>&#8203;> import torch
>&#8203;>&#8203;> x = torch.rand(5, 3)
>&#8203;>&#8203;> print(x) </code>
</pre>





## (Step 5, optional: unswap the swap)

Now if you like having 2GB of SWAP space (additional RAM basically, but a lot slower than your built-in RAM), then you are done. The downside is that you might run out of space later on. If you want to revert back to your old 500MB swap file then do the following:

Open the `/etc/fstab` file in the editor:

    sudo nano /etc/fstab


TODO


 please change the `/swap0` to `/swap1`. Then save the file with <key>CTRL</key>+<key>o</key> and <key>ENTER</key>. Close the editor with <key>CTRL</key>+<key>x</key>.
