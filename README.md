```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";


contract My721 is ERC721 {

    My20 my20;

    constructor(address _erc20Addr) ERC721("MyCollectible", "MCO") {
        my20 = My20(_erc20Addr);
        // _mint(msg.sender, 1); // owner, tokenId
    }

    function burn20(address _address, uint256 _amount) external {
        my20.burn(_address, _amount);
    }

    function mint20(address _address, uint256 _amount) external {
        my20.mint(_address, _amount);
    }
}

contract My20 is ERC20 {

    // a mapping from an address to whether or not it can mint / burn
    mapping(address => bool) controllers;
  
    constructor() ERC20("SuperToken", "STN") {
        controllers[msg.sender] = true;
        // _mint(msg.sender, 99); // owner, amount
    }

    function mint(address _to, uint256 _amount) external {
        require(controllers[msg.sender], "Only controllers can burn");
        _mint(_to, _amount);
    }

    function burn(address _from, uint256 _amount) external {
        require(controllers[msg.sender], "Only controllers can burn");
        _burn(_from, _amount);
    }

    function addController(address _controller) external {
        controllers[_controller] = true;
    }

    function removeController(address _controller) external {
        controllers[_controller] = false;
    }
}
```
