---
layout: post
title:  "Python, Numpy and Scipy installation from source without root access"
date:   2015-03-06 17:14:06
categories: guides
---

Python, as well as its numerical libraries are one of the essential toolsets for researchers and data scientists. But the installation process is not always so straightforward. In particular, there are some obstacles and pitfalls when you do not have the root priviledge to the servers. One solution to that is to install  python and the other libraries from sources code. However, the guides in scipy official site sometimes (or usually) does not work.  Here is how I did that with Python 2.7.

My server: CentOS 6.6, GCC 4.4

### Compile Python

Download Python 2.7 source code from the official site [Python-2.7.tgz](https://www.python.org/ftp/python/2.7/Python-2.7.tgz)

{% highlight bash %}
tar zxvf Python-2.7.tgz
cd Python-2.7
./configure --enable-shared --prefix=/home/your_account/Python27
make
make install
{% endhighlight %}

This would place a Python27 on your home directory. ```--enable-shared``` compiles libpython as shared object, which is critical if you want to invoke python code from C/C++ programs. 

Then add the path to environmental variable and setup the alias.

bash: edit .bashrc, add 

{% highlight bash %}
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/your_account/Python27/lib/   
alias python /home/your_account/Python27/bin/python
{% endhighlight %}

tcsh: edit .cshrc, add 

{% highlight bash %}
setenv LD_LIBRARY_PATH $LD_LIBRARY_PATH:/home/your_account/Python27/lib/   
alias python /home/your_account/Python27/bin/python
{% endhighlight %}

Then install the packages.

### nose

Diagnosis library for the others. Download sources from [here](https://pypi.python.org/pypi/nose/1.2.1).

{% highlight bash %}
tar zxvf nose-1.2.1.tar.gz
cd nose-1.2.1
python setup.py install --prefix=/home/your_account/Python27/
{% endhighlight %}

### lapack

Download [lapack-3.4.2.tgz](http://www.netlib.org/lapack/lapack-3.4.2.tgz). The installation will be combined with that of atlas.

### atlas

Download [atlas3.10.0.tar.bz2](http://downloads.sourceforge.net/project/math-atlas/Stable/3.10.0/atlas3.10.0.tar.bz2?r=http%3A%2F%2Fsourceforge.net%2Fprojects%2Fmath-atlas%2Ffiles%2FStable%2F3.10.0%2F&ts=1425823749&use_mirror=hivelocity). The detailed instruction is inside doc/ folder of the package. The steps:

{% highlight bash %}
tar jxvf atlas3.10.0.tar.bz2
cd ATLAS
mkdir build_obj
cd build_obj
../configure -b 64 -Fa alg -fPIC --shared --prefix=/home/your_account/path/to/ATLAS/build_obj/ --with-netlib-lapack-tarfile=/home/your_account/path/to/lapack-3.4.2.tgz
{% endhighlight %}

The configuration take some time. Then compile, check, and install.

{% highlight bash %}
make
make check
make time
make install
{% endhighlight %}

ATLAS is installed. Check out the fortran compiler used and remember it.

{% highlight bash %}
fgrep "F77=" Make.inc
{% endhighlight %}

You may see ```F77=gfortran```. So for my installation, the compiler is "gfortran". Remember yours for later references.

### numpy

Download [numpy-1.8.0.tar.gz](https://pypi.python.org/packages/source/n/numpy/numpy-1.8.0.tar.gz#md5=2a4b0423a758706d592abb6721ec8dcd). The fortran compiler indicated here and in the scipy installation **MUST** be consistent with the one your see in the last section, or mistakes will happen.

{% highlight bash %}
tar zxvf numpy-1.8.0.tar.gz
cd numpy-1.8.0
cp site.cfg.example site.cfg
{% endhighlight %}

edit site.cfg

{% highlight bash %}
[DEFAULT]
library_dirs = /usr/local/lib/:/home/your_account/path/to/ATLAS/build_obj/lib
include_dirs = /usr/local/include:/home/your_account/path/to/ATLAS/build_obj/include

[blas_opt]
libraries = f77blas, cblas, atlas

[lapack_opt]
libraries = lapack, f77blas, cblas, atlas

[amd]
amd_libs = amd

[umfpack]
umfpack_libs = umfpack
{% endhighlight %}

If your fortran compiler is "gfortran"

{% highlight bash %}
python setup build --fcompiler=gnu95
{% endhighlight %}

Otherwise, if your fortran compiler is "g77"

{% highlight bash %}
python setup build --fcompiler=gnu
{% endhighlight %}

Then finish the installation

{% highlight bash %}
python setup install --prefix=/home/your_account/Python27
{% endhighlight %}

# scipy

Download [scipy-0.13.0.tar.gz](http://downloads.sourceforge.net/project/scipy/scipy/0.13.0/scipy-0.13.0.tar.gz?r=http%3A%2F%2Fsourceforge.net%2Fprojects%2Fscipy%2Ffiles%2Fscipy%2F0.13.0%2F&ts=1425825899&use_mirror=iweb). Then steps are similar to those of numpy.

{% highlight bash %}
tar zxvf scipy-0.13.0.tar.gz
cd scipy-0.13.0.tar.gz
cp site.cfg.example site.cfg
{% endhighlight %}

edit site.cfg

{% highlight bash %}
[DEFAULT]
library_dirs = /usr/local/lib/:/home/your_account/path/to/ATLAS/build_obj/lib
include_dirs = /usr/local/include:/home/your_account/path/to/ATLAS/build_obj/include

[blas_opt]
libraries = f77blas, cblas, atlas

[lapack_opt]
libraries = lapack, f77blas, cblas, atlas

[amd]
amd_libs = amd

[umfpack]
umfpack_libs = umfpack
{% endhighlight %}

If your fortran compiler is "gfortran"

{% highlight bash %}
python setup build --fcompiler=gnu95
{% endhighlight %}

Otherwise, if your fortran compiler is "g77"

{% highlight bash %}
python setup build --fcompiler=gnu
{% endhighlight %}

Then finish the installation

{% highlight bash %}
python setup install --prefix=/home/your_account/Python27
{% endhighlight %}

### test for integrity

Start a python console

{% highlight python %}
import nose
import numpy
import scipy
numpy.test('full')
scipy.test('full')
{% endhighlight %}

### Install other packages

The installation for other packages are mostly trivial. Generally, download the source packages, unpack them, and type

{% highlight bash %}
python setup.py install --prefix=/home/your_account/Python27/
{% endhighlight %}

Most of them are fine with this. Some of packages may require [setuptools](https://pypi.python.org/pypi/setuptools).

For machine learning projects, these packages are particularly usefull:

[matplotlib](http://matplotlib.org/): matlab-like visualisation library

[scikit-learn](http://scikit-learn.org/stable/index.htmll): implementation of many usefull machine learning algorithms

[networkx](http://networkx.github.io/): good for graph and social network 

[pybrain](http://pybrain.org/): neural networks



