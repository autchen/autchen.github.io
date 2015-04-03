---
layout: post
title:  "Caffe installation on server without root access"
date:   Fri Apr  3 13:54:20 2015
categories: guides
---

[Caffe](caffe.berkeleyvision.org/installation.html) is a deep learning framework developed at BLVC. While the installation on Linux systems are straightforward with its guides, the dependencies make it hard to compile the codes without root accesses. This guide is about how I installed it into a CentOS 6.5 server while several library shipped are obsolete. Some of the tips are from [here](http://andrewjanowczyk.com/installing-caffe-on-the-ohio-super-computing-osc-ruby-cluster/).

Suppose we have a project folder that you can access as an user to the server. ```/home/you```. We create a folder ```/home/you/usr``` to hold all the dependencies. We first setup the environment variables.

{% highlight bash %}
setenv PATH /home/you/usr/bin/:$PATH
setenv LD_LIBRARY_PATH /home/you/usr/lib/:$LD_LIBRARY_PATH
{% endhighlight %}

### cmake

Usually it is shipped with the OS. But it may have a lower version than that some of the dependencies required. In case of that, download it from [here](http://www.cmake.org/download/). there is a compiled binary for Linux platform. Copy the files in the ```bin``` folder to ```/home/you/usr/bin```.

### CUDA

Luckily my CUDA runtime is 6.5 so I dont have to worry about that. CUDA 5 is fine, according to the official page, but remember you have to turn-off the ```gencode 2*``` flags from the caffe ```Makefile.config```.

### BLAS

I use ```ATLAS```, which is the default library for caffe. Please refer to [this article](http://autchen.github.io/guides/2015/03/06/python-install.html) for the compilation of ATLAS. If you dont have the compiled python availabe, follow the instructions as well.

### Boost

It seems quite a standard C++ library, but mine is 1.47 which is lower than the required version 1.55. Fine we can compile it! Download it from [here](http://www.boost.org/) and install it into your ```usr``` folder.

{% highlight bash %}
curl -L -O http://sourceforge.net/projects/boost/files/boost/1.57.0/boost_1_57_0.tar.gz
tar zxvf boost_1_57_0.tar.gz                                     
cd boost_1_57_0/                                                 
./bootstrap.sh --libdir=/home/you/usr/lib --includedir=/home/you/usr/include                                                                
vi project-config.jam # edit python path if you have a compiled one
./b2                                                             
./b2 install                                    
{% endhighlight %}

### OpenCV

If you dont have one, download it [here](docs.opencv.org/) and install.

{% highlight bash %}
curl -L -O http://sourceforge.net/projects/opencvlibrary/files/opencv-unix/2.4.10/opencv-2.4.10.zip 
unzip opencv-2.4.10.zip                                          
cd opencv-2.4.10
mkdir build                                                      
cd build                                                         
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/home/you/usr .. 
ccmake .. # optional: turn off some CUDA gencode for faster compilation, and type c
make                                                             
make install           
{% endhighlight %}

This will take very long time. Deleting some of the gencode of older architecures can improve it a little bit

### protobuf

Easy [https://github.com/google/protobuf](https://github.com/google/protobuf)

{% highlight bash %}
git clone https://github.com/google/protobuf.git                                                                                                              
cd protobuf/                                                     
./autogen.sh
./configure --prefix=/home/you/usr
make 
make install
{% endhighlight %}

### glog

Easy [https://github.com/google/glog](https://github.com/google/glog)

{% highlight bash %}
git clone https://github.com/google/glog.git
cd glog
./configure --prefix=/home/you/usr
make 
make install
{% endhighlight %}

### gflags

{% highlight bash %}
git clone https://github.com/gflags/gflags.git
cd gflags
mkdir build
cd build
ccmake .. # edit the prefix to /home/you/usr, and type c
vi CMakeCache.txt # edit CMAKE_CXX_FLAGS:STRING=-fPIC
make 
make install
{% endhighlight %}

This ```-fPIC``` step is important otherwise it come back to you when compiling caffe

### hdf5

I have that with my system, lucky. the offical site is [here](http://www.hdfgroup.org/HDF5/release/obtain5.html)

### leveldb

site [https://github.com/google/leveldb](https://github.com/google/leveldb)

{% highlight bash %}
git clone https://github.com/google/leveldb.git                  
cd leveldb/                                                      
make
cp --preserve=links libleveldb.* /home/you/usr/lib
cp -r include/leveldb /home/you/usr/include/          
{% endhighlight %}

It does not have ```make install```, copy them manually

### snappy

easy [https://github.com/google/snappy](https://github.com/google/snappy)

{% highlight bash %}
git clone https://github.com/google/snappy.git                                                                                                                
cd snappy/                                                       
./autogen.sh                                                     
./configure --prefix=/home/qchen/github/usr                      
make ; make install
{% endhighlight %}

### lmdb

site here [https://gitorious.org/mdb/mdb](https://gitorious.org/mdb/mdb)

{% highlight bash %}
git clone https://gitorious.org/mdb/mdb.git
cd mdb/libraries/liblmdb
vi Makefile # change prefix to /home/you/usr
make ; make install
{% endhighlight %}

### Finally, Caffe itself

Download the source codes

{% highlight bash %}
git clone https://github.com/BVLC/caffe.git
{% endhighlight %}

Go to the ```caffe``` folder, ```cp Makefile.config.example Makefile.config``` and edit it. Most of the instructions can be found on the offical installation page. Here is a few things that have to be specify

Setup the path to your ATLAS compilation

{% highlight make %}
BLAS := atlas                           
BLAS_INCLUDE := /path/to/your/atlas/include                                  
BLAS_LIB := /path/to/your/atlas/lib   
{% endhighlight %}

Setup path to your python installation

{% highlight make %}
PYTHON_INCLUDE := /path/to/your/python/include/python2.7 \
                  /path/to/your/python/lib/python2.7/site-packages/numpy/core/include
PYTHON_LIB := /home/qchen/Python27/lib
{% endhighlight %}

Add your dependencies to the makefile

{% highlight make %}
QC_CUSTOM_INCLUDE := /home/you/usr/include                              
QC_CUSTOM_LIB := /home/you/usr/lib                                      

INCLUDE_DIRS := $(PYTHON_INCLUDE) $(QC_CUSTOM_INCLUDE) /usr/local/include        
LIBRARY_DIRS := $(PYTHON_LIB) $(QC_CUSTOM_LIB) /usr/local/lib /usr/lib 
{% endhighlight %}

Then finally

{% highlight bash %}
make all
make test
make runtest
{% endhighlight %}

Another long wait ... Hopefully you see the success

