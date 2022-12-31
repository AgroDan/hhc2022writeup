# Clone with a Difference

This challenge focused on obtaining a git repository through an alternate means. Specifically changing from SSH to HTTPS.

!!! note
	We just need you to clone one repo: git clone git@haugfactory.com:asnowball/aws_scripts.git 
	This should be easy, right?

	Thing is: it doesn't seem to be working for me. This is a public repository though. I'm so confused!

	Please clone the repo and cat the README.md file.
	Then runtoanswer and tell us the last word of the README.md file!


Simple enough?

!!! abstract ""
	```
	bow@36e18804bfd6:~$ git clone git@haugfactory.com:asnowball/aws_scripts.git
	Cloning into 'aws_scripts'...
	The authenticity of host 'haugfactory.com (34.171.230.38)' can't be established.
	ECDSA key fingerprint is SHA256:CqJXHictW5q0bjAZOknUyA2zzRgSEJLmdMo4nPj5Tmw.
	Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
	Warning: Permanently added 'haugfactory.com,34.171.230.38' (ECDSA) to the list of known hosts.
	git@haugfactory.com: Permission denied (publickey).
	fatal: Could not read from remote repository.

	Please make sure you have the correct access rights
	and the repository exists.
	```

SSH isn't configured properly. So I'll use HTTP instead.

!!! success ""
	Answer: `git clone http://haugfactory.com/asnowball/aws_scripts.git`

At that point I can `cd` into the newly-created `aws_scripts` directory and run `cat README.md`.

It gives me a long markdown file, where the last word in the doc is `maintainers`.

I can then type `runtoanswer` and enter the word in to finish the challenge!

Note that even though it ends with a period, adding the period to the end would be an incorrect answer. Ask me how I know!