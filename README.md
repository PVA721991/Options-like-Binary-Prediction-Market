# Options-like-Binary-Prediction-Market
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract BinaryPredictionMarket {
    string public question;
    uint256 public endTime;
    bool public resolved;
    bool public outcome; // true = Yes

    mapping(address => uint256) public yesBets;
    mapping(address => uint256) public noBets;
    uint256 public totalYes;
    uint256 public totalNo;

    error MarketNotEnded();
    error AlreadyResolved();

    event BetPlaced(address indexed user, bool isYes, uint256 amount);
    event MarketResolved(bool outcome);

    constructor(string memory _question, uint256 _durationDays) {
        question = _question;
        endTime = block.timestamp + _durationDays * 1 days;
    }

    function betYes() public payable {
        if (block.timestamp > endTime) revert MarketNotEnded();
        yesBets[msg.sender] += msg.value;
        totalYes += msg.value;
        emit BetPlaced(msg.sender, true, msg.value);
    }

    function betNo() public payable {
        if (block.timestamp > endTime) revert MarketNotEnded();
        noBets[msg.sender] += msg.value;
        totalNo += msg.value;
        emit BetPlaced(msg.sender, false, msg.value);
    }

    function resolve(bool _outcome) public {
        if (block.timestamp < endTime) revert MarketNotEnded();
        if (resolved) revert AlreadyResolved();
        resolved = true;
        outcome = _outcome;
        emit MarketResolved(_outcome);
    }
}
