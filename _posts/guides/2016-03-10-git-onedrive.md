---
layout: post
title:  "Git with OneDrive/Dropbox"
date:   Thu Mar 10 16:31:39 2016
categories: guides
---

Sometimes I would like to backup my codes to the cloud, even for some teeny-tiny projects. Some of these codes, however, are either not ready for the public or too shameful to let others see, so that I would not bother using the Github public repositories with them. [Github for education](https://education.github.com/) offers free private repositories for students, but the number is limited to 7 and does not have very large space. That makes using OneDrive/Dropbox an option.

Here is an example. First we create a test project.

{% highlight bash %}
mkdir foo
cd foo
git init
echo "test" > README.md
{% endhighlight %}

Then commit the changes as usual.

{% highlight bash %}
git add --all
git commit -m "first commit"
{% endhighlight %}

Create an empty repository in the sync folder, and add it as a remote.

{% highlight bash %}
git init --bare ~/OneDrive/foo.git
git remote add OneDrive ~/OneDrive/foo.git
{% endhighlight %}

Push the code to the repo.

{% highlight bash %}
git push -u OneDrive master
{% endhighlight %}

The code should be synced to the cloud. You can access the codes from other terminals.

{% highlight bash %}
git pull
or
git clone ~/OneDrive/foo.git
{% endhighlight %}

The method should be fine for backup purpose. But,

* Do not push from multiple terminals simultaneously, the repo could be damaged.
* Do not use it for cooperations, unless you can shout to your partner you are gonna push.
* Make sure the changes are synced before pulling from other places.


