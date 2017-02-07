---
layout: post
title: TrendMicroCTF - VirusClicker (Offensive 200)
author: quanyang
description: TrendMicro CTF 2015 Offensive 200. VirusClicker Android APK.
tags: [CTF, TRENDMICRO, APK, ANDROID]
category: [CTF, TRENDMICRO, ANDROID]
excerpt: ""
comments: true
share: true
---
## Click Button! Attack Virus!
***Points:*** 200 ***Category:*** Offensive 

***Description:***
[VirusClicker]({{ site.url }}/resources/files/VirusClicker.apk)

MD5: 1a0462c2bc27bdd3a5045036c36c3e31

---
# Solution

After downloading the APK and installing it on my BlueStacks Emulator, you see this screen that requires us to press on the button for 10'000'000 times...

![]({{ site.url }}/resources/images/virusclicker.png){: height="300px" width="200px"}
![]({{ site.url }}/resources/images/10mclicks.png){: height="300px" width="200px"}

We first use apktool to decompile the apk into the respective smali code. 

![]({{ site.url }}/resources/images/apkdecode.png){: width="auto"}

The part of the code that we're interested in is the onTouchEvent that is called on every press.

{% highlight smali linenos %}
.method public onTouchEvent(Landroid/view/MotionEvent;)Z
    .locals 4
    const/4 v3, 0x1
    invoke-virtual {p1}, Landroid/view/MotionEvent;->getAction()I
    move-result v0
    packed-switch v0, :pswitch_data_0
    :cond_0
    :goto_0
    return v3
    :pswitch_0
    iput-boolean v3, p0, Lcom/tm/ctf/clicker/activity/c;->h:Z
    goto :goto_0
    :pswitch_1
    const/4 v0, 0x0
    iput-boolean v0, p0, Lcom/tm/ctf/clicker/activity/c;->h:Z
    iget v0, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    add-int/lit8 v0, v0, 0x1
    iput v0, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    invoke-static {}, Lcom/tm/ctf/clicker/a/a;->b()V
    const/16 v0, 0xeb9
    iget v1, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    if-eq v0, v1, :cond_1
    const/16 v0, 0x2717
    iget v1, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    if-eq v0, v1, :cond_1
    const v0, 0xe767
    iget v1, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    if-eq v0, v1, :cond_1
    const v0, 0x186a3
    iget v1, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    if-eq v0, v1, :cond_1
    const v0, 0x78e75
    iget v1, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    if-eq v0, v1, :cond_1
    const v0, 0xf4243
    iget v1, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    if-eq v0, v1, :cond_1
    const v0, 0x98967f
    iget v1, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    if-ne v0, v1, :cond_2
    :cond_1
    new-instance v0, Landroid/content/Intent;
    const-string v1, "com.tm.ctf.clicker.SCORE"
    invoke-direct {v0, v1}, Landroid/content/Intent;-><init>(Ljava/lang/String;)V
    const-string v1, "SCORE"
    iget v2, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    invoke-virtual {v0, v1, v2}, Landroid/content/Intent;->putExtra(Ljava/lang/String;I)Landroid/content/Intent;
    iget-object v1, p0, Lcom/tm/ctf/clicker/activity/c;->a:Landroid/content/Context;
    invoke-virtual {v1, v0}, Landroid/content/Context;->sendBroadcast(Landroid/content/Intent;)V
    :cond_2
    const v0, 0x989680
    iget v1, p0, Lcom/tm/ctf/clicker/activity/c;->g:I
    if-gt v0, v1, :cond_0
    invoke-static {}, Landroid/os/Message;->obtain()Landroid/os/Message;
    move-result-object v0
    new-instance v1, Ljava/lang/StringBuilder;
    iget-object v2, p0, Lcom/tm/ctf/clicker/activity/c;->q:Ljava/lang/String;
    invoke-static {v2}, Ljava/lang/String;->valueOf(Ljava/lang/Object;)Ljava/lang/String;
    move-result-object v2
    invoke-direct {v1, v2}, Ljava/lang/StringBuilder;-><init>(Ljava/lang/String;)V
    const-string v2, "Jh"
    invoke-virtual {v1, v2}, Ljava/lang/StringBuilder;->append(Ljava/lang/String;)Ljava/lang/StringBuilder;
    move-result-object v1
    invoke-virtual {v1}, Ljava/lang/StringBuilder;->toString()Ljava/lang/String;
    move-result-object v1
    iput-object v1, v0, Landroid/os/Message;->obj:Ljava/lang/Object;
    iget-object v1, p0, Lcom/tm/ctf/clicker/activity/c;->b:Landroid/os/Handler;
    invoke-virtual {v1, v0}, Landroid/os/Handler;->sendMessage(Landroid/os/Message;)Z
    goto :goto_0
    :pswitch_data_0
    .packed-switch 0x0
        :pswitch_0
        :pswitch_1
    .end packed-switch
	.end method 
{% endhighlight %}

From the code, we can see that something will happen after we hit 0x989680 (10'000'000) clicks.
Also, it seems that there are "mini-checkpoints" that you're suppose to hit at 0xeb9, 0x2717, 0xe767, 0x186a3, 0x78e75, 0xf4243, 0x98967f. What I am going to do is to patch the APK such that upon the first touch event, I am going to invoke all the necessary instructions to show me the flag.

{% highlight smali linenos %}
	const v0, 0x989680
    const v1, 0x989680
    if-gt v0, v1, :cond_0
{% endhighlight %}

The same for all the "mini-checkpoints". 
We then use apktool to recompile the smali code back to .apk and then use jarsigner to sign it with a key we generated.
![]({{ site.url }}/resources/images/apksign.png)
We then install the "patched" apk onto the BlueStacks emulator and tada! We got the flag!

![]({{ site.url }}/resources/images/virusflag.png){: width="200px"}

***TMCTF{Congrats_10MClicks}***

