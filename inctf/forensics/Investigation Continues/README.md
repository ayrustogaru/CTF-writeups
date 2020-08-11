AND the Investigation continues... What have you done Adam? ( ಠ ʖ̯ ಠ)
The challenge description is as below:

![Challenge Description](images/challenge.png)

**Question 1**: When was the last time Adam entered an incorrect password to login?

The `SAM` registry hive in windows will generally have the answer for this. Do we have it? Since we found out the profile in the [Investigation](insertlink) challenge. We will look at the `hivelist` directly.
```
volatility --profile=Win7SP1x64 -f windows.vmem hivelist
```
![list of registry hives](hivelist.PNG)

I was struck with two options - 
1. Dump the `SAM` registry and open it in Eric Zimmerman's `Registry Explorer` or,
2. list the values in the console and manually interpret the byte values to get the `filetime` of the `last invalid login`. 

But I learnt my lesson on not to trust my manual calculation abilities in the Investigation challenge, so I decided to go with option 1.

```
 volatility --profile=Win7SP1x64 -f windows.vmem dumpregistry -o 0xfffff8a0018f0410 -D=dumpdir/
```
![registry dump output](registry.PNG)

On opening the file in `Registry Explorer`, we can find the `last invalid login` time in `SAM\Domains\Account\Users`.

![hive values](login.PNG)

Therefore, the last incorrect password was entered by Adam on `22-07-2020 09:05:11`

**Question 2**: When was the file 1.jpg Opened?
TBC...
