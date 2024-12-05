# auction_frontend
Build a frontend for the auction contract using starknet scaffold.

# Auction Contract Frontend

This project provides a frontend interface for interacting with an **NFT Auction Contract** deployed on the StarkNet blockchain. Users can place bids, view the current highest bid, and end the auction if they are the owner.

---

## Prerequisites

### Tools and Dependencies

1. **Node.js and Yarn:**
   - Install Node.js (16.x or higher) and Yarn.
     ```bash
     npm install -g yarn
     ```

2. **StarkNet Devnet:**
   - Install and run StarkNet Devnet for testing:
     ```bash
     pip install starknet-devnet
     starknet-devnet
     ```

3. **StarkNet Scaffold:**
   - Clone the StarkNet Scaffold repository:
     ```bash
     git clone https://github.com/onlydustxyz/starknet-scaffold.git
     cd starknet-scaffold
     ```
   - Install dependencies:
     ```bash
     yarn
     ```

---

## Configuration

1. **Copy Environment File:**
   ```bash
   cp .env.example .env
   ```

2. **Set Contract Address:**
   Replace `<your_auction_contract_address>` in the `.env` file with the deployed auction contract address:
   ```env
   VITE_STARKNET_CONTRACT_ADDRESS=<your_auction_contract_address>
   ```

---

## Contract Interaction Methods

Add these methods in `src/lib/starknet.ts` to interact with the auction contract:

```typescript
import { Contract, Provider, defaultProvider } from "starknet";

const auctionAbi = require("../artifacts/auction_abi.json");
const auctionContractAddress = import.meta.env.VITE_STARKNET_CONTRACT_ADDRESS;

const provider = defaultProvider;
const contract = new Contract(auctionAbi, auctionContractAddress, provider);

export const placeBid = async (amount: number) => {
    const result = await contract.invoke("place_bid", [amount]);
    console.log("Bid placed:", result.transaction_hash);
};

export const endAuction = async () => {
    const result = await contract.invoke("end_auction");
    console.log("Auction ended:", result.transaction_hash);
};

export const getAuctionDetails = async () => {
    const details = await contract.call("get_auction_details");
    console.log("Auction Details:", details);
    return details;
};
```

---

## Auction Frontend Page

Create the auction interface in `src/pages/Auction.tsx`:

```tsx
import React, { useState, useEffect } from "react";
import { placeBid, endAuction, getAuctionDetails } from "../lib/starknet";

const Auction = () => {
    const [details, setDetails] = useState({ is_active: 0, highest_bid: 0, highest_bidder: 0 });
    const [bid, setBid] = useState(0);

    useEffect(() => {
        fetchAuctionDetails();
    }, []);

    const fetchAuctionDetails = async () => {
        const auctionDetails = await getAuctionDetails();
        setDetails(auctionDetails);
    };

    const handleBid = async () => {
        await placeBid(bid);
        fetchAuctionDetails();
    };

    const handleEndAuction = async () => {
        await endAuction();
        fetchAuctionDetails();
    };

    return (
        <div>
            <h1>Auction Contract</h1>
            <p>Auction Active: {details.is_active ? "Yes" : "No"}</p>
            <p>Highest Bid: {details.highest_bid}</p>
            <p>Highest Bidder: {details.highest_bidder}</p>
            <input
                type="number"
                placeholder="Enter your bid"
                value={bid}
                onChange={(e) => setBid(Number(e.target.value))}
            />
            <button onClick={handleBid}>Place Bid</button>
            <button onClick={handleEndAuction}>End Auction</button>
        </div>
    );
};

export default Auction;
```

---

## Add Routing

Update `src/App.tsx` to include the auction route:

```tsx
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import Auction from "./pages/Auction";

const App = () => {
    return (
        <Router>
            <Routes>
                <Route path="/auction" element={<Auction />} />
            </Routes>
        </Router>
    );
};

export default App;
```

---

## Run the Frontend

Start the development server:
```bash
yarn dev
```

Open your browser and navigate to `http://localhost:3000/auction` to interact with the auction contract.

---

## Testing

1. **Place Bids:**
   - Enter a bid amount and click **Place Bid**.
   - Verify the bid updates the highest bid.

2. **End Auction:**
   - Click **End Auction** (restricted to the contract owner).

3. **View Details:**
   - Ensure the highest bid and bidder are displayed correctly.

---



