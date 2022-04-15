# Ethernaut Fallback

Now we're getting into interacting with contracts using their abi

Take a gander at this contract right here.

What's wrong with it?

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Fallback {

  using SafeMath for uint256;
  mapping(address => uint) public contributions;
  address payable public owner;

  constructor() public {
    owner = msg.sender;
    contributions[msg.sender] = 1000 * (1 ether);
  }

  modifier onlyOwner {
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

  function getContribution() public view returns (uint) {
    return contributions[msg.sender];
  }

  function withdraw() public onlyOwner {
    owner.transfer(address(this).balance);
  }

  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}
```


The major hint here is fallback.

You'll be able to claim ownership of the contract if you call the recieve function with a value that is greater than 0 and if your address is in the contributions mapping.

You already know how to call functions on the contract using the abi but you may not know how to add some ether to the function call yet.

Here's how you can do that:

![](https://i.imgur.com/cqNp5qc.png)


```
await contract.functionName({value: yourValue})
```

Pretty cool right?

Next we're going to need to trigger that recieve function.

Recieve is a type of fallback function that is called when funds are sent directly to the contract.

We can send funds directly to the contract with a handy function named sendTransaction


![](https://i.imgur.com/4UHmwyy.png)

![](https://i.imgur.com/kAs27eY.png)


```
await contract.sendTransaction({value: yourValue})
```


After you've sent this transaction with a value, you will have claimed ownership of the contract & you've won!!

![](https://i.imgur.com/SIt3Z1S.png)
