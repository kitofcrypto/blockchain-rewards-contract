# blockchain-rewards-contract
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract RewardSystem is Ownable {
    IERC20 public rewardToken;
    mapping(address => uint256) public rewards;
    
    event RewardAssigned(address indexed user, uint256 amount);
    event RewardClaimed(address indexed user, uint256 amount);

    constructor(address _rewardToken) {
        rewardToken = IERC20(_rewardToken);
    }

    function assignReward(address user, uint256 amount) external onlyOwner {
        rewards[user] += amount;
        emit RewardAssigned(user, amount);
    }

    function claimReward() external {
        uint256 amount = rewards[msg.sender];
        require(amount > 0, "No rewards available");
        require(rewardToken.balanceOf(address(this)) >= amount, "Not enough rewards in contract");
        
        rewards[msg.sender] = 0;
        rewardToken.transfer(msg.sender, amount);
        
        emit RewardClaimed(msg.sender, amount);
    }
}
357
