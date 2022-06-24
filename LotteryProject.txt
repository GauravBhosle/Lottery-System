//SPDX-License-Identifier: GPL-3.0

pragma solidity >=0.5.0 <0.9.0;

contract Lottery{
    address public manager; // address because tansaction will be done using address only
    address payable[] public participants; // payable because participants will pay ether

    constructor(){
        manager=msg.sender; // msg.sender is gobal variable
    }

    receive() external payable{ // in-bulid function only calls once, payable because it will take ethers from participants 
        require(msg.value==1 ether); //below code will run only if amount paid to contaract is equal to 1 ether
        participants.push(payable(msg.sender)); //registering address of particiants who will join lottery
    }

    function getBalance() public view returns(uint){
        require(msg.sender==manager); //below code will run only if address is equal to manager's address
        return address(this).balance; // returns contract balance
    }

    function random() private view returns(uint){
       return uint(keccak256(abi.encodePacked(block.difficulty,block.timestamp,participants.length)));
       // this will genrate random numbers
    }

    function selectWinnner() public {
        require(msg.sender==manager); // B_C_W_O_IF manager is calling this function
        require(participants.length>=3); // B_C_W_O_IF participants are more thn 3
        uint r=random(); // call random fuction to get random value
        uint index=r%participants.length; //gives any number less than participants length
        address payable winner=participants[index]; // winner address is payable i.e money will come in this address
        winner.transfer(getBalance()); //money transer to winner address
        participants=new address payable[](0);
    }
}
