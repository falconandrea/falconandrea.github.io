# INK Testnet Faucet

This is a test project for creating a simple faucet on the INK testnet. The faucet allows developers to obtain test tokens for development purposes.

## Ink documentation

- [https://docs.inkonchain.com/quick-start/get-connected](https://docs.inkonchain.com/quick-start/get-connected)

## Link demo

- [https://falconandrea.github.io/ink-faucet/](https://falconandrea.github.io/ink-faucet/)

## Project Information

The faucet is designed to provide a maximum of 0.02 ETH per address every 24 hours. Users can connect their wallet and request test tokens through a simple web interface.

## Smart Contract Details

- **Contract Address:** 0xbc7B74caFEBbE79B01Ee19E77179db90e60bbf88
- **Explorer link:** [https://explorer-sepolia.inkonchain.com/address/0xbc7B74caFEBbE79B01Ee19E77179db90e60bbf88](https://explorer-sepolia.inkonchain.com/address/0xbc7B74caFEBbE79B01Ee19E77179db90e60bbf88)

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract Faucet {
    uint256 public constant amountAllowed = 0.02 ether;
    mapping(address => uint256) public lastRequestTime;
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    receive() external payable {}

    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can call this function");
        _;
    }

    function requestTokens(address payable recipient) external {
        require(recipient != address(0), "Invalid recipient address");
        
        require(address(this).balance >= amountAllowed, "Not enough funds in the faucet");
        
        require(
            block.timestamp - lastRequestTime[recipient] >= 24 hours, 
            "This address can only request tokens once every 24 hours"
        );

        lastRequestTime[recipient] = block.timestamp;
        
        (bool success, ) = recipient.call{value: amountAllowed}("");
        require(success, "Transfer failed");
    }

    function getBalance() external view returns (uint256) {
        return address(this).balance;
    }

    function withdrawAll() external onlyOwner {
        (bool success, ) = owner.call{value: address(this).balance}("");
        require(success, "Withdrawal failed");
    }
}
```

## Notes
- Make sure you are connected to the INK testnet (Chain ID: 763373).
- If you encounter any errors, try refreshing the page and reconnecting.

## Disclaimer

The faucet is a test project and should not be used in production.