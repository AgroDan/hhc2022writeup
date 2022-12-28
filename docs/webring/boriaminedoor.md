# Boria Mine Door

This was a challenge that involved bypassing checks for various input, as well as cool way of teaching you how to design your own `svg`'s. That was pretty neato.

This is the challenge:

![The Challenge](/img/webring/boria1.png)

These are the instructions:

![The Instructions](/img/webring/boria2.png)

Source of this shows that each box here is an iframe:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title></title>
    <link rel="stylesheet" href="[style.css](view-source:https://hhc22-novel.kringlecon.com/style.css)">
</head>
<body>
    <div class="parent">
        <div class="instructions">
            <img class="instructions-img" src="[instructions.png](view-source:https://hhc22-novel.kringlecon.com/instructions.png)"/>
            <button class="okayBtn">Got it.</button>
        </div>
        <div class="iframes">
            <iframe src='[/pin1](view-source:https://hhc22-novel.kringlecon.com/pin1)' class='pin1'></iframe>
            <iframe src='[/pin2](view-source:https://hhc22-novel.kringlecon.com/pin2)' class='pin2'></iframe>
            <iframe src='[/pin3](view-source:https://hhc22-novel.kringlecon.com/pin3)' class='pin3'></iframe>
            <iframe src='[/pin6](view-source:https://hhc22-novel.kringlecon.com/pin6)' class='pin6'></iframe>
            <iframe src='[/pin5](view-source:https://hhc22-novel.kringlecon.com/pin5)' class='pin5'></iframe>
            <iframe src='[/pin4](view-source:https://hhc22-novel.kringlecon.com/pin4)' class='pin4'></iframe>
        <img class='wiring' src='[wiring.png](view-source:https://hhc22-novel.kringlecon.com/wiring.png)'/>
            <canvas class='effects' width='744' height='422'></canvas>
            <button class='helpBtn'>?</button>
        </div>
    </div>
    <script src="[conduit.js](view-source:https://hhc22-novel.kringlecon.com/conduit.js)"></script>
    <script src="[app.js](view-source:https://hhc22-novel.kringlecon.com/app.js)"></script>
</body>
</html>
```

## Lock 1

Each iframe has source code. The first iframe `pin1` reads:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lock 1</title>
    <link rel="stylesheet" href="[pin.css](view-source:https://hhc22-novel.kringlecon.com/pin.css)">
</head>
<body>
    <form method='post' action='[pin1](view-source:https://hhc22-novel.kringlecon.com/pin1)'>
        <!-- @&@&&W&&W&&&& -->
        <input class='inputTxt' name='inputTxt' type='text' value='' autocomplete='off' />
        <button>GO</button>
    </form>
    <div class='output'></div>
    <img class='captured'/>
    
    <!-- js -->
    <script src='[pin.js](view-source:https://hhc22-novel.kringlecon.com/pin.js)'></script>
</body>
</html>
```

Note the comment. Is that the answer?

Pin 1 Answer: `@&@&&W&&W&&&&`

Yup!

## Lock 2

Source code for lock 2:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="Content-Security-Policy" content="default-src 'self';script-src 'self';style-src 'self' 'unsafe-inline'">
    <title>Lock 2</title>
    <link rel="stylesheet" href="[pin.css](view-source:https://hhc22-novel.kringlecon.com/pin.css)">
</head>
<body>
    <form method='post' action='[pin2](view-source:https://hhc22-novel.kringlecon.com/pin2)'>
        <!-- TODO: FILTER OUT HTML FROM USER INPUT -->
        <input class='inputTxt' name='inputTxt' type='text' value='' autocomplete='off' />
        <button>GO</button>
    </form>
    <div class='output'></div>
    <img class='captured'/>
    
    <script src='[js/cf3bd7e186bfea281b8076674c0fb22877715966.js](view-source:https://hhc22-novel.kringlecon.com/js/cf3bd7e186bfea281b8076674c0fb22877715966.js)'></script>
    <script src='[pin.js](view-source:https://hhc22-novel.kringlecon.com/pin.js)'></script>
</body>
</html>
```

Now...time to create some SVG images. This is apparently how you're supposed to get it to work! SVG will solve everything. Image drawing an image with text alone.

This worked for lock 2:
```xml
<svg version="1.1" width="200" height="170" xmlns="http://www.w3.org/2000/svg">
<rect x="0" y="0" width="100%" height="100%" fill="black" />
<line x1="1" x2="198" y1="73" y2="153" stroke="white" stroke-width="5" />
</svg>
```

## Lock 3

Source code for lock 3:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline'; style-src 'self'">
    <!-- <meta http-equiv="Content-Security-Policy" content="default-src 'self'; img-src https://*; child-src 'none';"> -->
    <title>Lock 3</title>
    <link rel="stylesheet" href="[pin.css](view-source:https://hhc22-novel.kringlecon.com/pin.css)">
</head>
<body>
    <form method='post' action='[pin3](view-source:https://hhc22-novel.kringlecon.com/pin3)'>
        <!-- TODO: FILTER OUT JAVASCRIPT FROM USER INPUT -->
        <input class='inputTxt' name='inputTxt' type='text' value='' autocomplete='off' />
        <button>GO</button>
    </form>
    <div class='output'></div>
    <img class='captured'/>
    
    <!-- js -->
    <script src='[pin.js](view-source:https://hhc22-novel.kringlecon.com/pin.js)'></script>
</body>
</html>
```

Looking at the image for Lock 3, I got the coordinates for the 2 blue connectors with a resulting image in GIMP:

![The GIMP](/img/webring/boria3.png)

This allowed me to find the X,Y coordinates, which are:

Lower Left: x=0, y=94
Upper Right: x=198, y=22

Now at the very least I can build a connector. Using this as input, I was able to get it to work without a problem:

```xml
<svg version="1.1" width="200" height="170" xmlns="http://www.w3.org/2000/svg">
<rect x="0" y="0" width="100%" height="100%" fill="black" />
<line x1="0" x2="198" y1="94" y2="22" stroke="blue" stroke-width="5" />
</svg>
```


## Lock 4

Source code for lock 4:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lock 4</title>
    <link rel="stylesheet" href="[pin.css](view-source:https://hhc22-novel.kringlecon.com/pin.css)">
    <script>
        const sanitizeInput = () => {
            const input = document.querySelector('.inputTxt');
            const content = input.value;
            input.value = content
                .replace(/"/, '')
                .replace(/'/, '')
                .replace(/</, '')
                .replace(/>/, '');
        }
    </script>
</head>
<body>
    <form method='post' action='[pin4](view-source:https://hhc22-novel.kringlecon.com/pin4)'>
        <input class='inputTxt' name='inputTxt' type='text' value='' autocomplete='off' onblur='sanitizeInput()' />
        <button>GO</button>
    </form>
    <div class='output'></div>
    <img class='captured'/>
    
    <!-- js -->
    <script src='[pin.js](view-source:https://hhc22-novel.kringlecon.com/pin.js)'></script>
</body>
</html>
```

Looks like we have some character replacement on the client side. It's not a global replace, so basically all I need to do is repeat the characters we're removing twice and it won't hit the second set. Also, best part of client side bypassing is that I control it!

But first, let's get the numbers necessary...

We will need two coords.

Line 1 (white):
	x=0, y=44
	x=198, y=44

Line 2 (blue):
	x=0, y=133
	x=198, y=133

Which gave me:

```xml
<svg version="1.1" width="200" height="170" xmlns="http://www.w3.org/2000/svg">
<rect x="0" y="0" width="100%" height="100%" fill="black" />
<line x1="0" x2="198" y1="44" y2="44" stroke="white" stroke-width="5" />
<line x1="0" x2="198" y1="133" y2="133" stroke="blue" stroke-width="5" />
</svg>
```

Now, I could go ahead and try to bypass the javascript altogether, but this works too: because I'm exploiting the concept of the replace function stopping me from using more than 1 `<` and `>` as well as `"`"`

```xml
<<svg version=""1.1" width="200" height="170" xmlns="http://www.w3.org/2000/svg">>
<rect x="0" y="0" width="100%" height="100%" fill="black" />
<line x1="0" x2="198" y1="44" y2="44" stroke="white" stroke-width="5" />
<line x1="0" x2="198" y1="133" y2="133" stroke="blue" stroke-width="5" />
</svg>
```

If I want to bypass user input, I could just disable it from my browser!

![Disable Input](/img/webring/boria4.png)

Now all I need to do is remove the `onBlur` attribute and I can enter the above SVG and be done with it. Easy!

## Lock 5

Source code for lock 5:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline'; style-src 'self'">
    <title>Lock 5</title>
    <link rel="stylesheet" href="[pin.css](view-source:https://hhc22-novel.kringlecon.com/pin.css)">
    <script>
        const sanitizeInput = () => {
            const input = document.querySelector('.inputTxt');
            const content = input.value;
            input.value = content
                .replace(/"/gi, '')
                .replace(/'/gi, '')
                .replace(/</gi, '')
                .replace(/>/gi, '');
        }
    </script>
</head>
<body>
    <form method='post' action='[pin5](view-source:https://hhc22-novel.kringlecon.com/pin5)'>
        <input class='inputTxt' name='inputTxt' type='text' value='' autocomplete='off' onblur='sanitizeInput()' />
        <button>GO</button>
    </form>
    <div class='output'></div>
    <img class='captured'/>
    
    <!-- js -->
    <script src='[pin.js](view-source:https://hhc22-novel.kringlecon.com/pin.js)'></script>
</body>
</html>
```

This has a global search and replace. Yet for some reason, a prepended space bypasses it entirely.

First let's get the coords of 5:

Line 1 (red):
	x=0, y=135
	x=198, y=43

Line 2 (blue)
	x=40, y=168
	x=198, y=89

Gives me:

```xml
<svg version="1.1" width="200" height="170" xmlns="http://www.w3.org/2000/svg">
<rect x="0" y="0" width="100%" height="100%" fill="black" />
<line x1="0" x2="198" y1="135" y2="43" stroke="red" stroke-width="5" />
<line x1="40" x2="198" y1="168" y2="89" stroke="blue" stroke-width="5" />
</svg>
```

THIS WORKS FOR SOME REASON, note the space in front!
```xml
 <svg version="1.1" width="200" height="170" xmlns="http://www.w3.org/2000/svg">
<rect x="0" y="0" width="100%" height="100%" fill="black" />
<line x1="0" x2="198" y1="135" y2="43" stroke="red" stroke-width="5" />
<line x1="40" x2="198" y1="168" y2="89" stroke="blue" stroke-width="5" />
</svg>
```


And all I had to do for pin5 again was to disable the same `sanitizeInput()` function in the source. Super easy, barely an inconvenience.

## Lock 6

Source for lock 6:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="Content-Security-Policy" content="script-src 'self'; style-src 'self'">
    <title>Lock 6</title>
    <link rel="stylesheet" href="[pin.css](view-source:https://hhc22-novel.kringlecon.com/pin.css)">
</head>
<body>
    <form method='post' action='[pin6](view-source:https://hhc22-novel.kringlecon.com/pin6)'>
        <input class='inputTxt' name='inputTxt' type='text' value='' autocomplete='off' />
        <button>GO</button>
    </form>
    <div class='output'></div>
    <img class='captured'/>
    
    <!-- js -->
    <script src='[pin.js](view-source:https://hhc22-novel.kringlecon.com/pin.js)'></script>
</body>
</html>
```

Pin 6 looks to do some sort of input sanitization on the backend I guess? I couldn't use `<script> document.write("blah");</script>` as something removed it, and there was no client side code for that. So could I just insert a random SVG?

The answer is yes, yes I can.

This one needed 3 lines.

Line 1 (green):
	x=0, y=34
	x=198, y=34

Line 2 (red):
	x=0, y=76
	x=198, y=109

Line 3 (blue)
	x=0, y=117
	x=146, y=168

Result:

```xml
<svg version="1.1" width="200" height="170" xmlns="http://www.w3.org/2000/svg">
<rect x="0" y="0" width="100%" height="100%" fill="black" />
<line x1="0" x2="198" y1="34" y2="34" stroke="#00FF00" stroke-width="5" />
<line x1="0" x2="198" y1="76" y2="109" stroke="red" stroke-width="5" />
<line x1="0" x2="146" y1="117" y2="168" stroke="blue" stroke-width="5" />
</svg>
```

NOTE: I had to change the stroke of the first line from green to `#00FF00` because the colors didn't match up. I screenshotted it and did a color match in GIMP to determine the RGB parameters needed. "Green" was too dark to register.

But this is what it looked like!

![The Completed Captcha](/img/webring/boria5.png)