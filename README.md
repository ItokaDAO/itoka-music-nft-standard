# Itoka music NFT Standard

We propose a new NFT standard based on Internet Computer Protocol(ICP) to serve the audio streaming and music copyright protection. The architecture is implemented on the top of Rocklabs' [`ic-nft`](https://github.com/rocklabs-io/ic-nft), which is a ERC721-like NFT implementation. The extended API functions include the assets encryption, streaming control and royalty collection etc. The goal of this project is leveraging the NFT power to build a transparent, trustless and permanent streaming protocol for the digital music assets. 

The first 72 genesis NFTs airdrop has been completed. 

:point_right: Itoka NFT canister ID: 4y4bz-6aaaa-aaaai-acj4a-cai

:point_right: Candid UI: https://a4gq6-oaaaa-aaaab-qaa4q-cai.raw.ic0.app/?id=4y4bz-6aaaa-aaaai-acj4a-cai

:point_right: Itoka µxive frontend: https://ku323-qyaaa-aaaai-ackgq-cai.ic0.app/airdrop

The project is under development. We are open for suggestions and community collaboration.

## NFT developement Roadmap(Draft)

- integrate ERC721-like NFT on IC canister with off-chain metadata
  - Deploy the pure ERC721 on IC by Motoko✔️
  - Add metadata and CDN from AWS ✔️
  - Add Internet Identity auth ✔️
  - Add frontend for mint/transfer and ledger ✔️
  - Deploy NFT on IC main-net ✔️
  - Add the NFT token in 3rd party wallet ✔️
- Upgrade from off-chain to on-chain 
  - Design metadata format ✔️
  - Design encryption mechanism ✔️
- Implement streaming control API
  - Implement the streaming ledger ✔️
  - Implement the listener authentication for streaming ✔️
  - Implement the listener authorization for streaming
- Implement royalty collection ledger and API
  - Design how to determine the royalty rate (set by custodian or DAO voting)
  - Enable the trustless royalty collection for Itoka NFT 
    - Accept royalty by $ICP
    - Accept royalty by $ITOKA
    - Accept royalty by BTC, ETH etc. once Dfinity completed BTC/ETH intergration 

## How to use?

### Prerequisites

1. `dfx` ^0.10.0 

## Setup

First clone `itoka-music-nft-standard` repo

```shell
git clone https://github.com/ItokaDAO/itoka-music-nft-standard.git
```

[Optional] We highly recommend to clone `Internet Identity` repo under the same directory for later testing

```shell
git clone https://github.com/dfinity/internet-identity.git
```

Run bash script to install dependency 

```shell
cd Itoka
npm install
sudo ./install.sh
```
Run run Python script to locally deploy canister and mint example NFTs

```shell
python mint_nft.py
```
Now you could check the API on default local Candid UI:  http://localhost:8000/?canisterId=ryjl3-tyaaa-aaaaa-aaaba-cai&id=rrkah-fqaaa-aaaaa-aaaaq-cai

## Metadata specifications

There are 3 audio assets for each single NFT song: 1. `.wav` raw soundtrack 2. `.map3` compressed soundtrack 3. `.mp3` compressed soundtrack for first 30 seconds preview. All assets are encrypted via `aes256`![link](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) algorithm. There is the sample code to demonstrate how to encrypt and decrypt asset by nodeJS:

```javascript
function generate_key() {
  // Defining key
  const key = crypto.randomBytes(32).toString("hex");

  // Defining iv
  const iv = crypto.randomBytes(16).toString("hex");
  return { iv, key };
}

function encrypt(algorithm, text, key, iv) {
  const cipher = crypto.createCipheriv(
    algorithm,
    Buffer.from(key, "hex"),
    Buffer.from(iv, "hex")
  );

  const encrypted = Buffer.concat([cipher.update(text), cipher.final()]);

  return {
    iv: iv.toString("hex"),
    content: encrypted.toString("hex"),
  };
}

function decrypt(algorithm, hash, secretKey) {
  const decipher = crypto.createDecipheriv(
    algorithm,
    Buffer.from(secretKey, "hex"),
    Buffer.from(hash.iv, "hex")
  );

  const decrpyted = Buffer.concat([
    decipher.update(Buffer.from(hash.content, "hex")),
    decipher.final(),
  ]);

  return decrpyted.toString();
}

// generate private key and iv
let temp = generate_key();
let prviateKey = temp.key;
let iv = temp.iv;

// read .wav
let buff = fs.readFileSync(wav_dir);
text = buff.toString("base64");

// Encryption
let hash_wav = encrypt("aes256", text, prviateKey, iv);
let text_back = decrypt("aes256", hash_wav, prviateKey);
console.log(text == text_back); // expected return True
```

# Reference

### NFT stardard

Appreciate the inspiration from Gigaverse lab(ICpunk & Market place): https://github.com/stopak/ICPunks/tree/master.

ERC721-like Motoko implementation on IC from Rocklabs: https://github.com/rocklabs-io/ic-nft

DIP721 Rust implementation from Psychedelic(Plug & DAB): https://github.com/Psychedelic/DIP721/blob/develop/src/main.rs

EXT Motoko implementation from Toniq-Labs(Stoic & Entrepot market place): https://github.com/Toniq-Labs/extendable-token/blob/main/examples/erc721.mo

### NFT IC registry

DAB Registry Standard: https://docs.dab.ooo/

### Frontend

Internet Identity authentication by frontend: https://github.com/krpeacock/auth-client-demo.git
