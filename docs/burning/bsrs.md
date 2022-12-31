# Bored Sporc Rowboat Society
This particular challenge was certainly unique in that it discussed the concept of Merkle Trees and its integration into the Non-Fungable Token market. Specifically, to ensure that only very specific buyers are allowed to purchase an NFT at the point of sale, a user is added to a Merkle Tree. This particular tree is used as a means of masking the members of this tree while at the same time being able to verify that someone is a member of the tree as well.

The Merkle Tree looks something like this:

![Merkle Tree](/img/burning/merkle.png)

Where a member of the tree would be on the lowest level, aka the "leaves" of the tree. Each parent consists of a hash of the two leaves concatenated together, where if we are `E` on this tree, `B` would be a hash of `D` and `E` together, and `A` would be a hash of both `B`and `C` together.

To verify if you are a member of this Merkle Tree, you would need your own hash (IE `E`), your neighbor's hash `D`, and the second half of the alternate branch from the root, `C`.  Therefore, if the Hash algorithm used can be represented as `H()`, then `H(H(D+E)+C) == A` then you have proven that you are a member of the Merkle Tree.

However, this only works if whatever service you are trying to confirm has complete control over what the root hash is (in the case of this example, `A`). We'll get to that in a minute...

## The Bored Sporc Rowboat Society

![BSRS Site](/img/burning/bsrssite.png)


A bunch of Crypto Bros have the ingenious idea of selling NFTs for next to nothing at the start of the sale, attempt to hype up the demand by involving social media influencers to buy in at a more expensive price, and when the hype has accelerated to obscene levels, dump the inventory at the highest price possible and get out while the gettin's good. This pump-and-dump scheme has been attempted before in various means, such as penny stocks and whatnot (See movies like `The Wolf of Wall Street`).

Our goal is to invade the BSRS group's first-buyer merkle tree and obtain our own unique NFT. If we can flood the market with NFTs, we may just save Christmas against unwitting buyers driving up the cost and ruining the KringleCoin economy. 

Luckily for us, Professor Qwerty Petabyte has given us some starter code via his [Github](https://github.com/QPetabyte/Merkle_Trees) that allows us to create our own Merkle Tree. To make things even easier, since it pulls libraries from other places, we can use the docker container to build it for us. But first, let's take a closer look at this site...

There is a Gallery included on the site showing the Wallet IDs of the owners of some of their first NFTs they had for sale.

![Gallery](/img/burning/gallery.png)

That's pretty convenient.

Then, they also had a `Presale` page that allowed me to check to see if I was on the list to make a presale.

!!! quote ""
	# Welcome to the Bored Sporc Rowboat Society Presale Page!

	The presale is only available to those select individuals who have earned a place on our exclusive presale list. If you're not on the list, you might as well leave, because you ain't gettin' a Sporc until we open up sales to the general public. If you are on the list, **_welcome!_**

	Here's all you gotta do to pre-purchase your Sporc:

	2.  The presale price for a Sporc is 100 KringleCoin (KC). Yeah, we know that's crazy cheap, but we take care of our buds. When we open sales to the public, these things are gonna shoot to the moon.
	3.  First, you're gonna want to make sure that your wallet address is on the approved list. Just make sure to leave the "Validate only" box checked, fill in the form, and we'll let you know if you're good-to-go. Before you do anything else, it's always good to be sure you're doing everything right and your address is validated as being on the list (it's actually something called a Merkle Tree... very high-techy-techy stuff).
	4.  To check if you're on the list, enter your wallet address and the string of proof values that we gave you when we told you that you were on the pre-approved list. Those values should be hex strings (i.e. start with "0x" and consist of a bunch of values that are 0-9 or "a," "b," "c," "d," "e," or "f"). If you're confused, give us a shout and we can help.
	5.  If you're not on the presale list, **_you're not on the list_**. Don't beg and plead with us to put you on the list. Seriously - we've only put Sporcs that we're tight with on the list. _**WE**_ decided who's on the list (COOL SPORCS ONLY). We don't just let **_anyone_** on. If we were putting you on the list, we would've contacted you... not the other way around.
	6.  Once you've confirmed everything works and you're sure you have the whole _validated-and-on-the-list_ thing down, just go find a KTM and pre-approve a 100 KC transaction from the wallet you validated. That way, the funds are ready to go. Our Wallet Address is 0xe8fC6f6a76BE243122E3d01A1c544F87f1264d3a.
	7.  Once you've pre-approved the payment, come back here do the same thing you did when you validated your address, just uncheck the "Validate Only" thing. Then, we'll grab your K'Coin, mint a brand spankin' new Sporc, and fire it into your wallet. Zap! Just like that, you'll be the owner of an amazing piece of the digital domain and a member of the Bored Sporc Rowboat Society for life! (Or, until you decide to cash-out and sell your Bored Sporc).


So similar to the Hat Vending Machine challenge, it looks like if I get the go-ahead to purchase, I need to go to the KTM and approve a transfer of 100KC to the wallet address of `0xe8fC6f6a76BE243122E3d01A1c544F87f1264d3a`. Now all I need to do is find out how to get on the list.

At the bottom of the page, it shows a form to check if I can purchase.

![Form](/img/burning/form.png)

Using my handy browser development tools, I can find out more about what data is sent here.

![Dev Tools](/img/burning/devtools.png)

At the right side of that image, you can see some of the values sent:

```json
{
	"WalletID":"test",
	"Root":"0x52cfdfdcba8efebabd9ecc2c60e6f482ab30bdc6acf8f9bd0600de83701e15f1",
	"Proof":"test",
	"Validate":"true",
	"Session":"0cf4c4c5-2bff-4cd8-997d-5d850b8a2f7e"
}
```

Look at that, there's the root hash of the Merkle Tree being sent with my test data. I never entered that! But it lets me send it anyway. Interesting!

## Professor Qwerty Petabyte's Merkle Tree Creator

Given the code shown on Prof. Petabyte's github page, I can first clone it locally:

!!! abstract ""
	`#!sh git clone https://github.com/QPetabyte/Merkle_Trees.git`

Then I can build it with `Docker`

!!! abstract ""
	`#!sh pushd Merkle_Trees >/dev/null && DOCKER_BUILDKIT=1 docker build -t merkletrees .`

```sh
╭─ ~/Documents/hhc2022/Merkle_Trees ~ main !1 
╰─ DOCKER_BUILDKIT=1 docker build -t merkletrees .
[+] Building 16.4s (9/9) FINISHED                                                                                                                                
 => [internal] load build definition from Dockerfile                                                                                                        0.0s
 => => transferring dockerfile: 519B                                                                                                                        0.0s
 => [internal] load .dockerignore                                                                                                                           0.0s
 => => transferring context: 2B                                                                                                                             0.0s
 => [internal] load metadata for docker.io/library/python:3.9-slim-bullseye                                                                                 0.8s
 => [1/4] FROM docker.io/library/python:3.9-slim-bullseye@sha256:9e0b4391fc41bc35c16caef4740736b6b349f6626fd14eba32793ae3c7b01908                           2.3s
 => => resolve docker.io/library/python:3.9-slim-bullseye@sha256:9e0b4391fc41bc35c16caef4740736b6b349f6626fd14eba32793ae3c7b01908                           0.0s
 => => sha256:9e0b4391fc41bc35c16caef4740736b6b349f6626fd14eba32793ae3c7b01908 1.86kB / 1.86kB                                                              0.0s
 => => sha256:e23b65d2ed7cbb4f9975bcc46421470ca670e8e47744fe4b03eb94d39311ad24 1.37kB / 1.37kB                                                              0.0s
 => => sha256:e2f4645510047ccdb2023834cae6d1c742faf045121d34c86f7646f6d991e5f2 7.48kB / 7.48kB                                                              0.0s
 => => sha256:3f4ca61aafcd4fc07267a105067db35c0f0ac630e1970f3cd0c7bf552780e985 31.40MB / 31.40MB                                                            0.7s
 => => sha256:3f487a3359db51ac4dbd797f76b61641328818f50fafc53d2cf760bc63584293 1.08MB / 1.08MB                                                              0.1s
 => => sha256:ae22731824bef1c5488b52d5ce19e9d977d0a904e7022a60efc7e976a0ef326d 11.58MB / 11.58MB                                                            0.4s
 => => sha256:3583ac268677b2697ee700b7eb8cf17aff67a415317d26ee8edf44455a3744ec 233B / 233B                                                                  0.2s
 => => sha256:de224c04316a139357db80c0585301d2cca14fcba9e0675d110f5228bc33adf9 3.18MB / 3.18MB                                                              0.5s
 => => extracting sha256:3f4ca61aafcd4fc07267a105067db35c0f0ac630e1970f3cd0c7bf552780e985                                                                   0.8s
 => => extracting sha256:3f487a3359db51ac4dbd797f76b61641328818f50fafc53d2cf760bc63584293                                                                   0.1s
 => => extracting sha256:ae22731824bef1c5488b52d5ce19e9d977d0a904e7022a60efc7e976a0ef326d                                                                   0.3s
 => => extracting sha256:3583ac268677b2697ee700b7eb8cf17aff67a415317d26ee8edf44455a3744ec                                                                   0.0s
 => => extracting sha256:de224c04316a139357db80c0585301d2cca14fcba9e0675d110f5228bc33adf9                                                                   0.1s
 => [internal] load build context                                                                                                                           0.0s
 => => transferring context: 7.72kB                                                                                                                         0.0s
 => [2/4] RUN apt-get update && apt-get upgrade -y && apt install vim nano -y && pip install eth_typing hexbytes web3 &&     useradd -m -s /bin/bash mt_u  11.7s
 => [3/4] COPY merkle_tree.py /home/mt_user/merkle_tree.py                                                                                                  0.0s
 => [4/4] WORKDIR /home/mt_user                                                                                                                             0.0s
 => exporting to image                                                                                                                                      1.5s 
 => => exporting layers                                                                                                                                     1.5s 
 => => writing image sha256:0dd159df289103c08ffaf857da5b64325f22dd51e60b7982824412618ef1b45b                                                                0.0s 
 => => naming to docker.io/library/merkletrees
```

Then start up into the container to edit.

!!! abstract ""
	`#!sh docker run -it --rm --name=merkletrees merkletrees`

From here, if I just run the command without changing anything, it outputs:

!!! abstract ""
	```
	mt_user@a4e736a841f4:~$ python ./merkle_tree.py
	Root: 0x431aa5796d9dcb4f660d5693a60130628c39fcbe6b83648a572929b1625f5332
	Proof: ['0x5380c7b7ae81a58eb98d9c78de4a1fd7fd9535fc953ed2be602daaa41767312a']
	mt_user@a4e736a841f4:~$ 
	```

Interesting...so it will create a merkle tree full of members and return a proof based on how many members are in it. Since I'm able to change the root hash at will, I wonder if I can just insert it into the request and hope that it uses my provided root hash to test for authenticity?

First, I'll modify these lines in the code:

```python linenums="139" hl_lines="11 12"

             if loc % 2 == 0 and loc != len(self.tree[i]) - 1:
                 proof.append(self.tree[i][loc + 1].hex())
                 lookup_val = Web3.solidityKeccak(['bytes32', 'bytes32'],[self.tree[i][loc], self.tree[i][loc + 1]])
             elif loc % 2 == 0 and loc == len(self.tree[i]) - 1:
                 pass
             else:
                 proof.append(self.tree[i][loc - 1].hex())
                 lookup_val = Web3.solidityKeccak(['bytes32', 'bytes32'],[self.tree[i][loc - 1], self.tree[i][loc]])
         return proof

#allowlist = ['0x1337133713371337133713371337133713371337','0x0000000000000000000000000000000000000000']
allowlist = ['0x0C32F9ED96F12fD7cd090870fd22fD4e53799428', '0x10Da24D355f5D8E63ce0b9e515841D510a74ff81']

leaves = []
for address in allowlist:
    leaves.append(Web3.solidityKeccak(['bytes'], [address]))
mt = MerkleTreeKeccak(leaves)
```

I will then add my wallet ID (`0x0C32F9ED96F12fD7cd090870fd22fD4e53799428`) and a known hash from the gallery, specifically the owner of this NFT:

![Pirate](/img/burning/pirate.png)

...just because that's a cool pirate hat....

And run the script to create a new Merkle Tree with a new root hash and Proof.

In theory, if I can modify the root hash, it will take a neighboring hash and compare it against the *supplied* root hash and see if it's a member of the Merkle Tree. I can do this by using the Browser's dev tools again!

Just go to the Network tab and send it while it's open, then right click on the request and choose "Edit and Resend"

![Edit and Resend](/img/burning/editandresend.png)

Now simply add my own values to the new request!
![Modify Form](/img/burning/modifyform.png)

```json
{
"WalletID":"0x0C32F9ED96F12fD7cd090870fd22fD4e53799428",
"Root":"0x33a523bd20240f2326a531ab9d9c70cd7f8c4d79e44f67134d6e8bfac19c4268",
"Proof":"0xbfba24158ac5ad568c56bf07fef5f0c702348b24bf5415ff974bac62d9125b2f",
"Validate":"true",
"Session":"<redacted>"
}
```

Since this was a validation only, it said that I was good to go! I just needed to go to the KTM and wire 100KC to `0xe8fC6f6a76BE243122E3d01A1c544F87f1264d3a`. 

![KTM](/img/burning/ktm.png)

Once I did that, I had to go back to the site and re-send, only this time disabling the Validation portion of it. Upon completion of that...

!!! success ""
	```json
	{
		"Response": "Success! You are now the proud owner of BSRS Token #000255. You can find more
		 information at https://boredsporcrowboatsociety.com/TOKENS/BSRS255, or check it out in the 
		 gallery!<br>Transaction: 0x63e34e691030a64222605424db88bf8b955f0b856f9f29cd7a2cc6f405c24a36, Block: 
		 74058<br><br>Remember: Just like we planned, tell everyone you know to <u><em>BUY A 
		 BoredSporc</em></u>.<br>When general sales start, and the humans start buying them up, the prices 
		 will skyrocket, and we all sell at once!<br><br>The market will tank, but we'll all be rich!!!"
	}
	```

My NFT!

![Sporc](/img/burning/BSRS255.png)