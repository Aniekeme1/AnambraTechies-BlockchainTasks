// SPDX-Licence-Identifier: MIT
pragma solidity >=0.7.0 <0.9.0;

contract myBank {
    mapping(address => uint256) public balances;
    address payable owner;
    mapping(address => bool) public admins;

    constructor() public {
        owner = payable(msg.sender);
        admins[msg.sender] = true;
    }

    function deposit() public payable {
        require(msg.value > 0, "Deposit amount must be greater than 0");
        balances[msg.sender] += msg.value;
    }

    modifier onlyOwner () {
        require(msg.sender == owner, "Only the owner can call this function");
        _;
    }

    modifier onlyAdmin () {
        require(admins[msg.sender], "Only Admins can call this function");
        _;
    }

    function withdraw(uint256 amount) public {
        require(msg.sender == owner, "Only the owner can withdraw funds");
        require(amount <= balances[msg.sender], "Insufficient funds");
        require(amount > 0, "Withdrawal amount must be greater than 0");
        balances[msg.sender] -= amount;
        address payable recepient = payable(msg.sender);
        balances[recepient] += amount;
    }

    function getBalance(address payable user) public view returns (uint256) {
        return balances[user];
    }

    function grantAccess(address payable user) public {
        require(msg.sender == owner, "Only the owner can grant access");
        owner = user;
    }

    function revokeAccess(address payable user) public {
        require(msg.sender == owner, "Only the owner can revoke access");
        require(user != owner, "Cannot revoke access for the current owner");
        owner = payable(msg.sender);
    }

    function destroy() public {
        require(msg.sender == owner, "Only the owner can destroy the contract");
        selfdestruct(owner);
    }
}
