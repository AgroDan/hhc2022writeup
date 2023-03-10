# Alabaster Snowball

Alabaster is back again this year!

![Alabaster Snowball](/img/webring/alabastersnowball.png){ align=left }

This time, instead of a terminal, I am given a PCAP file and am instructed (by Alibaster) to go threat hunting!

!!! quote "Alabaster Snowball"
	Hey there! I'm Alabaster Snowball

	And I have to say, I'm a bit distressed.

	I was working with the dwarves and their Boria mines, and I found some disturbing activity!

	Looking through [these artifacts](https://storage.googleapis.com/hhc22_player_assets/boriaArtifacts.zip), I think something naughty's going on.

	Can you please take a look and answer a few questions for me?

	First, we need to know where the attacker is coming from.

	If you haven't looked at Wireshark's _Statistics_ menu, this might be a good time!


uh...oddly enough, apparently I could levitate next to Alibaster? I'll chalk this one up to unexplainable Christmas Magic.

![I can levitate](/img/webring/levitate.png)

After reviewing the PCAP, I found the bad actor. Looking through HTTP traffic I found someone attempting to send malicious XML, as evidenced by the tell-tale XXE attack with the `SYSTEM` attribute in the payload:

![The Bad Actor](/img/webring/badactor.png)

!!! success "Answer to 'Naughty IP' Question"
	`18.222.86.32`

After finding it, Alabaster had this to say:

!!! quote "Alabaster Snowball"
	Aha, you found the naughty actor! Next, please look into the account brute force attack.

	You can focus on requests to `/login.html`~


I searched for `HTTP` packets, then did a "Find" for POST methods, since that is most likely the method of the first login. Sure enough, the first login was to login.html, and the username was `alice`

!!! success "Answer to 'Credential Mining' Question"
	`alice`

After that, Alabaster had this to add:

!!! quote "Alabaster Snowball"
	Alice? I totally expected Eve! Well how about forced browsing? What's the first URL path they found that way?

	The misses will have HTTP status code `404` and, in this case, the successful guesses return `200`.

For this next question, I had to find the next successful discover from a content discovery tool, and to do this I looked for a slew of `404 FIle Not Found` errors, then looked for the next `200 OK` using a simple search. The first successful directory from the IP of `18.222.86.32` was `/proc`

!!! success "Answer to '404 FTW' Question"
	`/proc`

Alabaster had one more challenge:

!!! quote "Alabaster Snowball"
	Great! Just one more challenge! It looks like they made the server pull credentials from IMDS. What URL was forced?

	AWS uses a specific IP address for IMDS lookups. Searching for that in the PCAP should get you there quickly.


Using the Statistics tab, I did a Protocol Hierarchy search and found XML. When I focused on the XML requests, only XXE showed up. As a result, I found the following back-and-forth request:

![XXE](/img/webring/xxe.png)

!!! success "Answer to 'IMDS, XXE, and Other Abbreviations' Question"
	`http://169.254.169.254/latest/meta-data/identity-credentials/ec2/security-credentials/ec2-instance`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY id SYSTEM "http://169.254.169.254/latest/meta-data/identity-credentials/ec2/security-credentials/ec2-instance"> ]>
<product><productId>&id;</productId></product>
HTTP/1.1 200 OK
```

Finally, Alabaster had this to say:

!!! quote "Alabaster Snowball"
	Fantastic! It seems simpler now that I've seen it once. Thanks for showing me!

	Hey, so maybe I can help you out a bit with the door to the mines.

	First, it'd be great to bring an Elvish keyboard, but if you can't find one, I'm sure other input will do.

	Instead, take a minute to read the HTML/JavaScript source and consider how the locks are processed.

	Next, take a look at the `Content-Security-Policy` header. That drives how certain content is handled.

	Lastly, remember that input sanitization might happen on either the client or server ends!

Looks like our next challenge will involve dealing with some client-side methods.