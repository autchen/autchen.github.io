---
layout: post
title:  "Python and package installation on MAC"
date:   2015-03-06 17:14:06
categories: guides
---

This guide assume root access to the computer. Otherwise, refer to [this article](http://autchen.github.io/guides/2015/03/06/python-install.html) for non-root installation from source code.

Normally, the installation is much straightforward on Linux, either apt-get or yum can help resolve the dependencies. For Windows users, here are the pre-compiled packages available [http://www.lfd.uci.edu/~gohlke/pythonlibs/](http://www.lfd.uci.edu/~gohlke/pythonlibs/). The installation on MAC is not difficult, but there might be some small troubles that make you not happy. 

### Install xcode for developer

Download and install xcode from apple app store, then open a terminal and type

{% highlight bash %}
xcode-select --install
{% endhighlight %}

Type gcc in the terminal to check the tools are properly installed

### Macports

This guide uses macports, which can be find [here](https://www.macports.org/). Remember to select the right version for your system or it wont work. Then in the terminal, type

{% highlight bash %}
sudo port selfupdate
{% endhighlight %}

### Install python and basic scientific librarys

Install the scipy stack

{% highlight bash %}
sudo port install py27-numpy py27-scipy py27-matplotlib py27-ipython +notebook py27-pandas py27-sympy py27-nose
{% endhighlight %}

This may take a while. After the installation finished, all the packages will be located in ```/opt/local/```, which is the default installation path of macports. Since MAC OS shipped a python, we have to change that

{% highlight bash %}
sudo port select --set python python27
sudo port select --set cython cython27
sudo port select --set sphinx py27-sphinx
sudo port select --set ipython ipython27
sudo port select --set py-sympy py27sympy
{% endhighlight %}

This will make ```python``` point to the right version.

### Other package

To find other packages with macport, type

{% highlight bash %}
port search scikit-learn
{% endhighlight %}

which gives you

{% highlight bash %}
py-scikit-learn @0.15.2 (python, science)
        Easy-to-use and general-purpose machine learning in Python

py26-scikit-learn @0.15.2 (python, science)
        Easy-to-use and general-purpose machine learning in Python

py27-scikit-learn @0.15.2 (python, science)
        Easy-to-use and general-purpose machine learning in Python

py32-scikit-learn @0.15.2_1 (python)
        this port is only a stub and has been made obsolete by py34-scikit-learn

py33-scikit-learn @0.14.1_1 (python, science)
        Easy-to-use and general-purpose machine learning in Python

py34-scikit-learn @0.15.2 (python, science)
        Easy-to-use and general-purpose machine learning in Python
{% endhighlight %}

For the other package not in the repository, use

{% highlight bash %}
python setup.py install --user
{% endhighlight %}

This places the files in ```~/Library```. This is important, otherwise the files mess up the macport tree while not notifying it.

[homebrew](http://brew.sh/) can achieve this similarly. It does not conflict with macport so you can have them both in the system. Normally, I use macport for python exclusively and homebrew for the other software.
