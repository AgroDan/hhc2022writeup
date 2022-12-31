# Blockchain Divination
###### Using the Blockchain Explorer

This challenge asked us to use the Blockchain Explorer in the Burning Ring of Fire to investigate the contracts and transactions on the chain. At what address is the KringleCoin smart contract deployed?

[Tom Liston's Talk](https://www.youtube.com/watch?v=r3zj9DPC8VY) is a good place to start on understanding this, but the basic gist of it is that this Blockchain is an Ethereum blockchain, which is an open source Blockchain which allows anyone to write Smart Contracts, which allows the programmer to write specific financial tasks automatically based on market conditions (or whatever). The language used is [Solidity](https://learnxinyminutes.com/docs/solidity/), and it is used to create the concept of KringleCoin!

For this exercise, we are going to use the handy Blockchain Explorer application to give us a visual explanation for each transaction involving KringleCoin at KringleCon. This is an example of what it looks like:

![Blockchain Explorer](/img/burning/blockchainexplorer.png)

If we scroll further down, we can see more of the meat that makes up this particular transaction. Specifically we can see the `translation` row along with the `Solidity Source Code` row, which gives the code used for this particular transation.

![Transaction](/img/burning/transaction.png)

Specifically, we see that this particular transaction looks like 5 KringleCoins were donated to a specific wallet for completing the `Orientation` challenge.

At the top of the page I can view a specific transaction on the Blockchain. If I'm to look for the Smart Contract that started the idea of KringleCoin, it should be relatively early into the transaction history. Let's view block 1 on the chain.

![Root Transaction](/img/burning/roottransaction.png)

Note: Block 0 is referred to as the "Genesis Block." You can review the final challenge writeup in [my 2020 Holiday Hack Challenge Writeup](https://hhc2020.agrohacksstuff.io/#obj11a/) to learn more about that. Block 1 however shows the original contract along with the source code, and the contact address assigned to it!

