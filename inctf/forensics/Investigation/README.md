We are provided with a file name `windows.vmem` which is a Windows memory dump file.

![Challenge Description](images/challenge.png)

**Step 1**: Find the profile of the memory dump using the `imageinfo` plugin
```
volatility imageinfo -f windows.vmem
```
![imageinfo output](images/imageinfo.PNG)
**Step 2**: Once we get the profile, check if the calculator was running when the dump was created. This can be done using the `pslist` plugin.
```
volatility --profile=Win7SP1x64 -f windows.vmem pslist
```
![process list](images/pslist.PNG)
Unfortunately, the `calc.exe` wasn't running. It would've been easy to get the timestamp from this output.

**Step 3**: Windows Registry!! One of the most informative and most difficult part to analyze forensically. First check the hives using `hivelist` plugin.
```
volatility --profile=Win7SP1x64 -f windows.vmem hivelist
```
![registry hive list](images/hivelist.PNG)

We have a NTUSER.DAT for the user Adam. This is kinda a log file that contains user's settings confs etc.

**Step 4**: Take a leap of faith and analyze the UserAssist\{...}\Count registry using the `userassist` plugin in volatility3 (Use the plugin's for god's sake, I was doing it
manually and the last run time I came up with was one second later). You could also dump the `NTUSER.DAT` using `dumpregistry` and then use Eric Zimmerman's `Registry Explorer` 
to skim through it.
```
volatility --profile=Win7SP1x64 -f windows.vmem userassist
```
![userassist count](images/userassist.PNG)

Locate the calc.exe and find out the last run time.
![calc.exe values](images/calc.PNG)

Then locate the Chrome and find the run count.
![chrome.exe values](images/chrome.PNG)

Therefore, the flag is `inctf{22-07-2020_18:21:35_19}
