---
title: "Make terminal upload file using file.io"
tags: ["shell", "linux"]
style:
color:
image: "/assets/images/post_3_fileioupload/main.jpg"
description: "Upload files using terminal without signup."
permalink: upload-file-from-terminal
---

![terminal]({{site.baseurl}}/assets/images/post_3_fileioupload/main.jpg)

> _**“The art of programming is the skill of controlling complexity.”**_ – Marijn Haverbeke

One day I needed to send some confidential file without signup. I googled about such service available on the internet and found [file.io](https://file.io).

According to **file.io** website -

{% include elements/highlight.html text="Simply upload a file, share the link, and after it is downloaded, the file is completely deleted. For added security, set an expiration on the file and it is deleted within a certain amount of time, even if it was never downloaded. All files are encrypted when stored on our servers." %}

Most of my time goes into hitting commands on the terminal. [file.io](https://file.io) can be accessed using any browser but I wanted to take this functionality to the terminal. I browsed that website and found an easy to use API.

At first, I tried to add a command for uploading files using an **alias** but it didn't work very fine. Then I thought of writing function which will be foolproof and give some errors for helping out the user if they type something wrong.

Before going ahead make sure you have **curl** installed.
If you are using **Ubuntu**, do this -
{% highlight shell %}
sudo apt-get install curl
{% endhighlight %}

## Add the functionality Automatically

Run this command in your terminal.

**For bash**
{% highlight shell %}
curl -L https://git.io/JeNX1 >> ~/.bashrc
source ~/.bashrc
{% endhighlight %}
**For zsh**
{% highlight shell %}
curl -L https://git.io/JeNX1 >> ~/.zshrc
source ~/.zshrc
{% endhighlight %}

## Add the functionality Manually

Add this to your **~/.bashrc** or **~/.zsh** file and restart your terminal. I tested this on **_bash and zsh in Ubuntu 18.04 LTS_**.

{% highlight shell %}

# Upload file via terminal using file.io

# Author : Aman Roy (https://github.com/aman-roy)

upload () { if [ $# = 1 -o $# = 2 ];then if [ -f "$1" ];then if [ $# = 1 ];then curl -F "file=@$1" https://file.io/ ;else if [[ "$2" =~ ^[1-9]+[wmy]$ ]];then curl -F "file=@$1" https://file.io/\?expires=$2;else echo $'Wrong expiration format.\neg. 1(w/m/y), etc.';fi;fi;else echo "file doesn't exist";fi;else echo $'usage: upload file_name.ext [expiration]\nexpiration format: 1-9(w/m/y) # (w)eeks, m(onths), (y)ear';fi }
{% endhighlight %}

## Usage

For simple file upload, do this
{% highlight shell %}
upload file.ext
{% endhighlight %}
<sub><sup>I am assuming here that the file name is **file.ext**. </sup></sub>

You can also _set an expiration period_. By default, the expiration is set to 14 days. Pass 3<sup>rd</sup> argument for expiration. You can write any positive number followed by **w, m or y**. w is for weeks, m for months and y for years.
{% highlight shell %}
upload file.ext 3w # Keeps the data on server for 21 days.
upload file.ext 2m # Keeps the data on server for 60 days.
upload file.ext 1y # Keeps the data on server for 365 days.
{% endhighlight %}

### Conclusion

It is very useful when you have to send some small sensitive data to another person. You can be assured that the file you are sending to another person will be deleted after he/she download it. The only drawback is that you can't send a large file**(file_size < 100MB)**.
Try this script on different shell out there and if you find any bug please let me know.
