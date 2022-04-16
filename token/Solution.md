This contract represents a token that has a total supply, & can be transferred between accounts.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Token {

  mapping(address => uint) balances;
  uint public totalSupply;

  constructor(uint _initialSupply) public {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public view returns (uint balance) {
    return balances[_owner];
  }
}
```

On deployment, your wallet will contain 20 tokens, how can we get our hands on more?

This phrase is given to us as a clue: "What is an odometer"

Earlier cars had odometers that would reach a numerical limit, of lets say n miles.

After getting to n miles they would "roll over" and go back to 0, like an integer overflow

To exploit this contract we can underflow the uint inside of the balances mapping.

This is possible because this contract doesn't have any math checks.

After solidity 0.8,  overflows/underflows are checked by default, however we don't have this luxury in 0.6.

To make this contract secure, the developers should use the openzepplin safemath library

Here are the steps for solving this challenge:

Transfer out more tokens than you own, and you will have successfully tricked the bookkeeping in this contract to think you have a high amount of tokens. 

This can be done in the chrome console

```
//replace with any address that isn't player
await contract.transfer('0xBe83934d557AF054E0330671DB7Cd15F45569BD9', 21)
```


<img src="https://i.imgur.com/k385EFd.png"/>
