# Suricata Regata

NOTE: I didn't realize I was supposed to add my own suricata ID, or else things go bananas. I just chose today's date (2022121101) and added an incremental number to it

![Suricata Regata Terminal](/img/tolkienring/suricataregata.png)

!!! note
	Use your investigative analysis skills and the suspicious.pcap file to help develop Suricata rules for the elves!

	There's a short list of rules started in suricata.rules in your home directory.

	First off, the STINC (Santa's Team of Intelligent Naughty Catchers) has a lead for us.
	They have some Dridex indicators of compromise to check out.
	First, please create a Suricata rule to catch DNS lookups for adv.epostoday.uk.
	Whenever there's a match, the alert message (msg) should read Known bad DNS lookup, possible Dridex infection.
	Add your rule to suricata.rules

	Once you think you have it right, run ./rule_checker to see how you've done!
	As you get rules correct, rule_checker will ask for more to be added.

	If you want to start fresh, you can exit the terminal and start again or cp suricata.rules.backup suricata.rules

	Good luck, and thanks for helping save the North Pole!

!!! success ""
	Answer: `alert dns any any -> any any (msg:"Known bad DNS lookup, possible Dridex infection"; dns.query; content:"adv.epostoday.uk"; nocase; sid:2022121101;)`

```
elf@0a65e9273829:~$ ./rule_checker 
11/12/2022 -- 15:44:34 - <Notice> - This is Suricata version 6.0.8 RELEASE running in USER mode
11/12/2022 -- 15:44:34 - <Notice> - all 9 packet processing threads, 4 management threads initialized, engine started.
11/12/2022 -- 15:44:34 - <Notice> - Signal Received.  Stopping engine.
11/12/2022 -- 15:44:34 - <Notice> - Pcap-file module read 1 files, 5172 packets, 3941260 bytes
First rule looks good!

STINC thanks you for your work with that DNS record! In this PCAP, it points to 192.185.57.242.
Develop a Suricata rule that alerts whenever the infected IP address 192.185.57.242 communicates with internal systems over HTTP.
When there's a match, the message (msg) should read Investigate suspicious connections, possible Dridex infection
```

!!! success ""
	Answer `alert http 192.185.57.242 any <> $HOME_NET any (msg:"Investigate suspicious connections, possible Dridex infection"; sid:2022121102)`

(Note the fact that I used bi-directional, sessions are not included in suricata, this is packet-analysis only)

Also, for some reason if I remove the `sid:1;` section from the first rule I wrote, it doesn't catch the second rule, but STILL catches the first. WTF

!!! quote "Future Agr0_Dan"
	this is because I have to give each rule it's own suricata ID or things just don't work.

```
elf@0a65e9273829:~$ ./rule_checker 
11/12/2022 -- 15:55:25 - <Notice> - This is Suricata version 6.0.8 RELEASE running in USER mode
11/12/2022 -- 15:55:25 - <Notice> - all 9 packet processing threads, 4 management threads initialized, engine started.
11/12/2022 -- 15:55:25 - <Notice> - Signal Received.  Stopping engine.
11/12/2022 -- 15:55:25 - <Notice> - Pcap-file module read 1 files, 5172 packets, 3941260 bytes
First rule looks good!

Second rule looks good!

We heard that some naughty actors are using TLS certificates with a specific CN.
Develop a Suricata rule to match and alert on an SSL certificate for heardbellith.Icanwepeh.nagoya.
When your rule matches, the message (msg) should read Investigate bad certificates, possible Dridex infection

For the third indicator, we flagged 0 packet(s), but we expected 1. Please try again!
```

!!! success ""
	Answer: `alert tls any any -> any any (msg:"Investigate bad certificates, possible Dridex infection"; tls.cert_subject; content:"CN=heardbellith.Icanwepeh.nagoya"; nocase; sid:2022121103;)`


```
elf@c058db4bcade:~$ ./rule_checker 
11/12/2022 -- 16:27:17 - <Notice> - This is Suricata version 6.0.8 RELEASE running in USER mode
11/12/2022 -- 16:27:17 - <Notice> - all 9 packet processing threads, 4 management threads initialized, engine started.
11/12/2022 -- 16:27:17 - <Notice> - Signal Received.  Stopping engine.
11/12/2022 -- 16:27:17 - <Notice> - Pcap-file module read 1 files, 5172 packets, 3941260 bytes
First rule looks good!

Second rule looks good!

Third rule looks good!

OK, one more to rule them all and in the darkness find them.
Let's watch for one line from the JavaScript: let byteCharacters = atob
Oh, and that string might be GZip compressed - I hope that's OK!
Just in case they try this again, please alert on that HTTP data with message Suspicious JavaScript function, possible Dridex infection
```

!!! success ""
	Answer: `alert http any any <> any any (msg:"Suspicious JavaScript function, possible Dridex infection"; file_data; content:"let byteCharacters = atob"; sid:2022121104;)`

End result: Christmas will be saved yet again