# Hat Vending Machine

This challenge was fairly cut and dry. I had to simply buy a hat from the vending machine next to Wombley Cube. Unlike a standard vending machine however, this machine only took KringleCoin, and it wasn't as simple as just slipping a bill into the little electronic roller slot. This machine had a digital wallet address which would only give you a hat if you sent it a certain amount of KC to it with a Hat ID, then asked it to approve the transaction ONLY after you approved yours first.

Unintuitive, thy name is Cryptocurrency.

Because I'm a sucker for bowler hats, I rooted through the inventory and found this one:

![Chosen Hat](/img/burning/chosenhat.png)

OK. So now I just need to amble over to the KTM and send 10 KC to that wallet address.

I went to the KTM, clicked on `Approve a KringleCoin Transfer` and was met with this screen:

![KTM Transfer](/img/burning/ktmtransfer.png)

Fairly straightforward (sorta). Just put the above mentioned address (`0xd47ae7f1D2eFfaE735834D11D4fc1031d71EaD19`) in the "To" field, and the amount being 10 KC, followed by my key.


![Hat Purchase](/img/burning/hatpurchase.png)

After a few seconds waiting for the transaction to finish, I was finally met with the message `You have successfully approved the transaction!`

And okay, we're done here. I have successfully transferred the money to the vending machine and now I have 10 KC of credit.

Back to the Hat Vending machine, I opened it and clicked on `Approved a transaction? Know your Hat ID? Click here to buy`

I knew that my Hat ID was `623`, and I know what my wallet ID is, so I entered them here:

![Hat Purchase 2](/img/burning/hatpurchase2.png)

Man, the KTM and Hat Vending Machine's UIs look remarkably similar...

After about 10 to 20 seconds later, I received my hat and transaction ID, which was located on Block `101725`.

![Transaction Succeeded](/img/burning/transactionsucceeded.png)

I can even confirm that by checking out the Blockchain Explorer!

![Transaction Verified](/img/burning/transactionverified.png)

Confirmed the transaction! As seen in the `translation` row.

## On a Side Note

Before I really got to understanding the final challenge, I thought I could find Luigi's hat that he was wearing in the vending machine and assume I could find the wallet ID from there. Alas, his hat is super unique. No fedoras anywhere! I feel short changed.

If only I could feel the same amount of smug that you have, Luigi.

![Luigi Closeup](/img/burning/luigi-closeup.png)