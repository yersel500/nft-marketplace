![](https://img.shields.io/badge/YERSEL-HURTADO-success)

# NFT Marketplace- Back-End

<img src="https://img001.prntscr.com/file/img001/cgQIwjfFT0iSdf4H_82yMA.png" width="600" alt="NFT Marketplace">

NFT Marketplace is a decentralized shop where users can:

  - `listItem`: List NFTs on the marketplace
  - `buyItem`: Buy the NFTs
  - `cancelItem`: Cancel a listing
  - `updateListing`: Update Price
  - `withdrawProceeds`: Withdraw payment for my bought NFTs

It contains two pages:

1. Home Page

   - Show recently listed NFTs
     - If you own the NFT, you can update the listing
     - If not, you can buy the listing

2. Sell Page:

   - You can list your NFT on the marketplace

## Front-end Repository

You can find the front-end [here](https://github.com/yersel500/nft-marketplace-frontend).

## Built With

- **Solidity**
- **Moralis**
- **NextJS**
- **JavaScript**
- **Visual Studio Code**

## Video Live Version

[Live Version](https://sparkling-butterfly-2231.on.fleek.co/)

# Full-Stack Setup

## 1. Git clone the contracts repo

In it's own terminal / command line, run in two separates terminal:

```
git clone git@github.com:yersel500/nft-marketplace.git
cd nft-marketplace
yarn
```

```
git clone git@github.com:yersel500/nft-marketplace-frontend.git
cd nft-marketplace-frontend
yarn
```

## 2. Start your node
Backend:
- `PRIVATE_KEY`: The private key of your account (like from [metamask](https://metamask.io/)). **NOTE:** FOR DEVELOPMENT, PLEASE USE A KEY THAT DOESN'T HAVE ANY REAL FUNDS ASSOCIATED WITH IT.
  - You can [learn how to export it here](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key).
```
PRIVATE_KEY=XXXXX
COINMARKETCAP_API_KEY=XXXXX
```

After installing dependencies with `yarn install`, start a node in the backned on it's own terminal with:

```
yarn hardhat node
```

The next steps from 3 to 7 are in the Frontend repo

## 3. Connect your codebase to your moralis server

Frontend:

Setup your event [moralis](https://moralis.io/). You'll need a new moralis server to get started.

Sign up for a [free account here](https://moralis.io/).

Once setup, update / create your `.env` file. You can use `.env.example` as a boilerplate.

```
NEXT_PUBLIC_APP_ID=XXXX
NEXT_PUBLIC_SERVER_URL=XXXX
moralisApiKey=XXX
moralisSubdomain=XXX
masterKey=XXX
chainId=31337
```

With the values from your account.

Then, in your `./package.json` update the following lines:

```
"moralis:sync": "moralis-admin-cli connect-local-devchain --chain hardhat --moralisSubdomain XXX.usemoralis.com --frpcPath ./frp/frpc",
"moralis:cloud": "moralis-admin-cli watch-cloud-folder  --moralisSubdomain XXX.usemoralis.com --autoSave 1 --moralisCloudfolder ./cloudFunctions"
```

Replace the `XXX.usemoralis.com` with your subdomain, like `4444acatycat.usemoralis.com` and update the `moralis:sync` script's path to your instance of `frp` (downloaded as part of the Moralis "Devchain Proxy Server" instructions mentioned above)

## 4. Globally install the `moralis-admin-cli`

```
yarn global add moralis-admin-cli
```

## 5. Setup your Moralis reverse proxy

> Optionally: On your server, click on "View Details" and then "Devchain Proxy Server" and follow the instructions. You'll want to use the `hardhat` connection.

- Download the latest reverse proxy code from [FRP](https://github.com/fatedier/frp/releases) and add the binary to `./frp/frpc`.
- Replace your content in `frpc.ini`, based on your devchain. You can find the information on the `DevChain Proxy Server` tab of your moralis server.

In some Windows Versions, FRP could be blocked by firewall, just use a older release, for example frp_0.34.3_windows_386

Mac / Windows Troubleshooting: https://docs.moralis.io/faq#frpc

Once you've got all this, you can run:

```
yarn moralis:sync
```

You'll know you've done it right when you can see a green `connected` button after hitting the refresh symbol next to `DISCONNECTED`. _You'll want to keep this connection running_.

<img src="https://img001.prntscr.com/file/img001/YthylDoaQsi6eGYVlGZ3ww.png" width="600" alt="Connected to Moralis Reverse Proxy">

### IMPORTANT

Anytime you reset your hardhat node, you'll need to press the `RESET LOCAL CHAIN` button on your UI!

## 6. Setup your Cloud functions

In a separate terminal (you'll have a few up throughout these steps)

Run `yarn moralis:cloud` in one terminal. If you don't have `moralis-admin-cli` installed already, install it globally with `yarn global add moralis-admin-cli`.

> Note: You can stop these after running them once if your server is at max CPU capactity.

If you hit the little down arrow in your server, then hit `Cloud Functions` you should see text in there.

<img src="https://img001.prntscr.com/file/img001/8news-vyRkmdtBIH8JInMw.png" width="500" alt="Cloud Functions Up">

Make sure you've run `yarn moralis:sync` from the previous step to connect your local Hardhat devchain with your Moralis instance. You'll need these 3 moralis commands running at the same time.

## 7. Add your event listeners

Frontend:

### You can do this programatically by running:

```
node addEvents.js
```

### Or, if you want to do it manually

Finally, go to `View Details` -> `Sync` and hit `Add New Sync` -> `Sync and Watch Contract Events`

Add all 3 events by adding it's information, like so:

1. ItemListed:
   1. Description: ItemListed
   2. Sync_historical: True
   3. Topic: ItemListed(address,address,uint256,uint256)
   4. Abi:

```
{
  "anonymous": false,
  "inputs": [
    {
      "indexed": true,
      "internalType": "address",
      "name": "seller",
      "type": "address"
    },
    {
      "indexed": true,
      "internalType": "address",
      "name": "nftAddress",
      "type": "address"
    },
    {
      "indexed": true,
      "internalType": "uint256",
      "name": "tokenId",
      "type": "uint256"
    },
    {
      "indexed": false,
      "internalType": "uint256",
      "name": "price",
      "type": "uint256"
    }
  ],
  "name": "ItemListed",
  "type": "event"
}
```

    5. Address: <YOUR_NFT_MARKETPLACE_DEPLOYED_ADDRESS_FROM_HARDHAT>
    6. TableName: ItemListed

You can add the canceled and bought events later.

## 8. Mint and List your NFT

Backend:

Back in the main directory, run:

```
yarn hardhat run scripts/mint-and-list-item.js --network localhost
```

And you'll now have an NFT listed on your marketplace.

## 9. Start your front end

At this point, you'll have a few terminals running:

- Your Hardhat Node
- Your Hardhat Node syncing with your moralis server
- Your Cloud Functions syncing
- Your Moralis Logging

And you're about to have one more for your front end.

```
yarn run dev
```

And you'll have your front end, indexing service running, and blockchain running.

## Author

👤 **Yersel Hurtado**

- GitHub: [@yersel500](https://github.com/yersel500/)
- LinkedIn: [Yersel Hurtado](https://www.linkedin.com/in/yersel-hurtado/)
- Twitter: [@YerselHurtado](https://twitter.com/YerselHurtado)

## 🤝 Contributing

Contributions, issues, and feature requests are welcome and highly encouraged!
We feel that your input is what helps us grow so you're always Welcome :)

Feel free to check the [issues page](../../issues/).

## Show your support

Give a ⭐️ if you like this project!