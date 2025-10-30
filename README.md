# KaiKongs NFT Marketplace

A complete NFT marketplace smart contract system built with Hardhat, Solidity, and TypeScript. This project enables users to create customizable NFT collections, mint tokens, and trade them through a fully-featured marketplace with listing, offering, and auction capabilities.

## 📋 Table of Contents

- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Smart Contracts](#smart-contracts)
- [Deployment](#deployment)
- [Testing](#testing)
- [License](#license)

## ✨ Features

### NFT Collection Factory
- **Dynamic Collection Creation**: Create custom ERC721 NFT collections with configurable parameters
- **Royalty Support**: Built-in royalty mechanism (up to 10%) for creators
- **Flexible Minting**: Owner can mint for free, users can mint up to 3 NFTs at once
- **IPFS Integration**: Metadata stored on IPFS with automatic URI handling
- **Pausable**: Emergency pause functionality for collection owners

### Marketplace Features
- **Direct Listing & Sales**: List NFTs at fixed prices and enable instant purchases
- **Bulk Buying**: Purchase multiple NFTs in a single transaction
- **Offer System**: Make and accept offers on listed NFTs
- **Auction System**: Create time-based auctions with minimum bid increments
- **Upgradeable**: UUPS proxy pattern for contract upgrades
- **Platform Fees**: Configurable marketplace fee (up to 10%)
- **Automatic Royalty Distribution**: Royalties automatically distributed to creators on secondary sales

## 🏗️ Architecture

```
├── contracts/
│   ├── KaiKongs.sol              # ERC721 NFT contract with royalties
│   ├── KaiKongFactory.sol        # Factory for creating NFT collections
│   ├── marketplace.sol           # Main marketplace contract (upgradeable)
│   └── interfaces/
│       ├── IKaiKongsFactory.sol  # Factory interface
│       └── IKaiKongsNFT.sol      # NFT interface
├── scripts/
│   ├── deploy.ts                 # Deployment script
│   ├── mint.ts                   # Minting script
│   └── test.ts                   # Testing script
└── test/
    └── Lock.ts                   # Test suite
```

## 📦 Prerequisites

- Node.js (v14+ recommended)
- Yarn or npm
- MetaMask or another Web3 wallet
- An Ethereum development environment (Hardhat is included)

## 🚀 Installation

1. Clone the repository:
```bash
git clone https://github.com/superdev947/KD-KingDom-SmartContract.git
cd KD-KingDom-SmartContract
```

2. Install dependencies:
```bash
yarn install
# or
npm install
```

3. Compile contracts:
```bash
npx hardhat compile
```

## 💻 Usage

### Deploying Contracts

Deploy the complete system (Factory + Marketplace + Sample Collection):

```bash
npx hardhat run scripts/deploy.ts --network <network-name>
```

### Creating an NFT Collection

```typescript
await kaiKongsFactory.createCollection(
  "Collection Name",        // name
  "SYMBOL",                 // symbol
  1000,                     // royaltyFee (in basis points, 1000 = 1%)
  royaltyRecipient,         // address to receive royalties
  ethers.utils.parseEther("0.1"), // mint price
  10000,                    // max supply
  "ipfs://YOUR_CID/"        // base URI for metadata
);
```

### Minting NFTs

```typescript
// Owner can mint for free
await nft.mint(recipientAddress, 3);

// Users must pay mint price (max 3 per transaction)
await nft.mint(userAddress, 2, { 
  value: ethers.utils.parseEther("0.2") 
});
```

### Listing NFT for Sale

```typescript
// Approve marketplace
await nft.approve(marketplaceAddress, tokenId);

// Create listing
await marketplace.createSell(
  nftAddress,
  tokenId,
  ethers.utils.parseEther("1.0"), // price
  sellerAddress
);
```

### Buying NFTs

```typescript
// Single purchase
await marketplace.buy(nftAddress, tokenId, buyerAddress, {
  value: ethers.utils.parseEther("1.0")
});

// Bulk purchase
await marketplace.bulkBuy(
  [nftAddress, nftAddress],
  [tokenId1, tokenId2],
  { value: totalPrice }
);
```

### Making Offers

```typescript
await marketplace.makeOffer(
  nftAddress,
  tokenId,
  offerPrice,
  { value: offerPrice }
);

// Seller accepts offer
await marketplace.acceptOfferNFT(nftAddress, tokenId, offererAddress);
```

### Creating Auctions

```typescript
// Approve NFT transfer
await nft.approve(marketplaceAddress, tokenId);

// Create auction
await marketplace.createAuction(
  nftAddress,
  tokenId,
  ethers.utils.parseEther("1.0"),  // starting price
  ethers.utils.parseEther("0.1"),  // minimum bid increment
  startTime,                       // auction start timestamp
  endTime                          // auction end timestamp
);

// Place bid
await marketplace.placeBid(
  nftAddress,
  tokenId,
  bidPrice,
  { value: bidPrice }
);

// Complete auction (after end time)
await marketplace.completeBid(nftAddress, tokenId);
```

## 📝 Smart Contracts

### KaiKongs.sol
ERC721-based NFT contract with the following features:
- ERC721Enumerable for token enumeration
- Pausable functionality
- Customizable royalties (up to 10%)
- IPFS metadata integration
- Owner-controlled minting

**Key Functions:**
- `mint(address to, uint256 amount)`: Mint NFTs
- `updateRoyaltyFee(uint256 _royaltyFee)`: Update royalty percentage
- `pause()/unpause()`: Emergency controls

### KaiKongsFactory.sol
Factory contract for creating and managing NFT collections.

**Key Functions:**
- `createCollection(...)`: Deploy new NFT collection
- `importCollection(address _address)`: Import existing collection
- `getUserCollections(address account)`: Get user's collections
- `isKaiKongsNFT(address _nft)`: Verify collection authenticity

### KaiKongsMarketplace.sol (Upgradeable)
Full-featured NFT marketplace with listing, offering, and auction capabilities.

**Key Functions:**
- `createSell()`: List NFT for sale
- `buy()`: Purchase listed NFT
- `bulkBuy()`: Purchase multiple NFTs
- `makeOffer()/acceptOfferNFT()`: Offer system
- `createAuction()/placeBid()/completeBid()`: Auction system
- `updatePlatformFee()`: Update marketplace fee

## 🔧 Configuration

The project uses Hardhat with the following configuration:

```typescript
{
  solidity: "0.8.18",
  optimizer: {
    enabled: true,
    runs: 200
  }
}
```

### Key Dependencies
- **OpenZeppelin Contracts**: Standard implementations for ERC721, Ownable, Pausable, etc.
- **OpenZeppelin Upgradeable**: UUPS proxy pattern for marketplace upgrades
- **Hardhat**: Development environment and testing framework
- **Ethers.js**: Ethereum library for contract interaction

## 🚢 Deployment

The deployment script (`scripts/deploy.ts`) deploys:

1. **KaiKongsFactory**: Factory contract for NFT collections
2. **KaiKongsMarketplace**: Upgradeable marketplace (UUPS proxy)
3. **Sample Collection**: Example "KaiKongs" collection
4. **Demo Listings**: Two sample NFT listings

Platform fee is set to 10% (10000 basis points) in the deployment script.

## 🧪 Testing

Run the test suite:

```bash
npx hardhat test
```

Run tests with coverage:

```bash
npx hardhat coverage
```

## 📊 Fee Structure

### Platform Fees
- Configurable up to 10%
- Applied to all sales (listings, offers, auctions)
- Paid to fee recipient address

### Royalty Fees
- Set per collection (up to 10%)
- Automatically distributed to royalty recipient
- Applied to all secondary sales

**Fee Calculation:**
```
Total Payment = Sale Price
Royalty = (Sale Price × Royalty Fee) / 100000
Platform Fee = (Sale Price × Platform Fee) / 100000
Seller Receives = Sale Price - Royalty - Platform Fee
```

## 🔒 Security Features

- **ReentrancyGuard**: Protection against reentrancy attacks
- **Access Control**: Ownable pattern for admin functions
- **Pausable**: Emergency stop mechanism
- **Upgradeable**: UUPS proxy for bug fixes and improvements
- **Input Validation**: Comprehensive requirement checks
- **Safe Transfers**: ERC721 safe transfer mechanisms
