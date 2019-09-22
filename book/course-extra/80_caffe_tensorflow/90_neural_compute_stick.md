# Movidius Neural Compute Stick Install {#ncsdk-install status=ready}

## Laptop Installation
install based on [ncsdk website](https://movidius.github.io/ncsdk/install.html)


    git clone http://github.com/Movidius/ncsdk
    cd ~/ncsdk
    make install
    make examples


test installation

    cd ~/ncsdk/examples/app/hello_ncs_py/
    make run


## Duckiebot Installation

you only need to install the NCSDK but there is also the option of installing Caffe and/or Tensorflow as well, in order to perhaps speed up the development cycle. I would recommend against it, as it can be a bigger problem than it solves.

### Barebones Install (recommended)
you don't need tensorflow, caffe, or any tools in order to run the compiled networks and not installing them will save you a lot of hassle

on duckiebot:

    git clone http://github.com/Movidius/ncsdk
    cd ~/ncsdk/api/src
    make
    sudo make install


### Caffe/Tensorflow Install

Note: if you want to be able to compile your models on the duckiebot itself, install tensorflow or caffe beforehand and remember to install for python 3 (`pip3`)

follow directions [here](#caffe-tensorflow-install)

make sure caffe and tensorflow as installed

    python3 -c 'import tensorflow as tf; import caffe'


install sdk:

    git clone http://github.com/Movidius/ncsdk
    cd ~/ncsdk
    make install
    make examples


# How To Use Neural Compute Stick {#ncsdk-how-to status=ready}

## Workflow

create and train model in tensorflow or caffe (brief note on [configuration](https://movidius.github.io/ncsdk/configure_network.html) )

save tensorflow model as a `.meta`  (or caffe model in `.prototxt`)

    saver = tf.train.Saver()
    ...
    saver.save(sess, '![model]')

compile the model into NC format (documentation [here](https://movidius.github.io/ncsdk/tools/compile.html))


    mvNCCompile ![model].meta -o ![model].graph


move model onto duckiebot

    scp ![model].meta ![user]@![robot name]:~/path_to_networks/

run the compiled model

    with open(path_to_networks + ![model].meta, mode='rb') as f:
        graphfile = f.read()
    graph = device.AllocateGraph(graphfile)
    graph.LoadTensor(input_image.astype(numpy.float16), 'user object')
    output, userobj = graph.GetResult()

## Benchmarking

get benchmarking (frames per second) from their app zoo

    git clone https://github.com/movidius/ncappzoo
    cd ncappzoo/apps/benchmarkncs
    ./mobilenets_benchmark.sh | grep FPSk
