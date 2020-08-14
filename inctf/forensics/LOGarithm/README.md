The challenge was as given below:

![Challenge Description](images/challenge.png)


A file named `Evidence.vmem` was provided to us. While exploring it using volatility. W.L.G, scan for the profile using the `imageinfo` plugin.

I decided to use the `screenshot` plugin to see if I can stumble upon any information.
```volatility --profile=Win7SP1x64 -f Evidence/Evidence.vmem screenshot -D shots/```



One of the screenshots was as follows:

![screenshot](images/screenshot.png)

As you can see, the `cmd.exe`, `Chrome` and `Downloads` windows are open and the `Downloads` points to the `C:\Users\Mike\Downloads` directory. So, out of curiosity, I looked at his browsing history and also scanned through the files present in that directory.

```volatility --plugins=plugins/ --profile=Win7SP1x64 -f Evidence/Evidence.vmem chromehistory```

![chrome history](images/history.PNG)

From the image you can see that Mike opened spam email

```volatility --profile=Win7SP1x64 -f Evidence/Evidence.vmem filescan | grep 'Users\Mike\Downloads'```

ANDDD 

![filescan output](images/filescan.PNG)

- downloaded a keylogger!! We can get the physical offset from here to dump the file.

Now, its time to check if the keylogger was actully used. List the processes running using `pslist`.

```volatility --profile=Win7SP1x64 -f Evidence/Evidence.vmem pslist```

![processes list](images/pslist.PNG)

and hah!! we have pythonw.exe running. Let's take a look at what was run with the `pythonw.exe`.

```volatility --profile=Win7SP1x64 -f Evidence/Evidence.vmem cmdline -p 2216```

![cmdline output](images/cmdline.PNG)

OK, beautiful!! the keylogger was running. Now, dump the keylogger and look at how the keystrokes were being sent to the adversary.

```volatility --profile=Win7SP1x64 -f Evidence/Evidence.vmem dumpfiles -Q 0x000000003ee119b0 -D dumpdir/```

![dumpfiles output](images/dumpfile.PNG)

You can look at `keylogger.py`'s code [here](keylogger.py). The keystrokes were being sent to the IP address `18.140.60.203` and port `1337`.
The keystrokes were being XORed with a key that is stored as an environment variable named `t3mp`. Then it is being encoded to base64.

The information that was sent can be found using the pcap file. The packets that were sent are:

![packets list](images/packets.PNG)

The data that was sent to the adversary can be found by following the conversation:

![data sent](images/conversation.PNG)

Let's write a simple decrypter. Basically 
