+++
author = "Manuel Levi"
categories = ["Crypto"]
date = 2022-05-01T11:35:00Z
description = ""
draft = true
image = "__GHOST_URL__/content/images/2022/05/stillness-inmotion-d-dYgV9pgMw-unsplash.jpeg"
slug = "an-unacceptable-contract"
tags = ["Crypto"]
title = "An  unacceptable  contract"

+++


It has been a while since I've touched any solidity code.

Being a bit tired of doing data science and artificial intelligence for so long, I decided to polish up my smart contract skills by reviewing a presale smart contract. Hopefully, I'll be able to identify some problems and propose solutions to those same problems.

While I review these smart contracts, I'll also create a short checklist for my smart-contract development. Learn from others' mistakes and prevent your own.

## What is a presale smart contract?

The concept is simple.

Some crypto projects offer their tokens for sale while the project is still under development and sometimes even before development starts. It's called a presale because they typically happen before the main token distribution.

Sometimes access to a presale is restricted, other times it's not. For the sake of simplicity, I've chosen to study a simple presale smart contract that does not implement a whitelist or any process to filter investors.

## The contract

I'm using MarginSwap's presale smart contract, which you can find [here](https://etherscan.io/address/0x6c8fbbf8e079246a92e760d440793f2f864a26b3#code).

<pre class="editor">/**
*Submitted for verification at Etherscan.io on 2021-02-03
*/

contract Presale {

    IERC20 public MFI;
    // these aren't ether, we're just using this for unit conversion
    uint public constant presaleSupply = 4_000_000 ether;
    // how much the presale has already issued
    uint public presaleIssued = 0;
    address public treasury;
    address constant USDC = 0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48;
    address constant uniRouter = 0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D;
    address constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

    uint public startDate;
    uint public lastVestedQuarter;
    // 1_500_000 / 8
    uint public constant vestingQuarterly = 187_500 ether;

    // check for reentrancy
    bool disbursing;

    // best guess ETH price
    uint public constant dollarsPerETH = 1552;
    uint public constant tokensPerDollar = 4;
    uint public constant tokensPerETH = dollarsPerETH * tokensPerDollar;

    uint public constant maxPerWallet = 5 ether * dollarsPerETH * tokensPerDollar;

    constructor(IERC20 tokenContract, uint _startDate, address _treasury) public {
        MFI = tokenContract;
        treasury = _treasury;
        startDate = _startDate;
    }  

    receive() external payable {
        // rule out reentrancy
        require(!disbursing, "No re-entrancy");
        disbursing = true;

        // check time constraints
        // after start date
        require(block.timestamp >= startDate, "Presale hasn't started yet");
        uint endDate = startDate + 2 days;
        // before end date
        require(endDate >= block.timestamp, "Presale is over");

        // no overflow, again because scarcity
        uint tokensRequested = msg.value * tokensPerETH;

        // calculate how much the sender actually gets
        uint tokensToTransfer = min(tokensRequested, // price
                                    sub(presaleSupply, presaleIssued), // don't exceed supply
                                    sub(maxPerWallet, MFI.balanceOf(msg.sender))); // don't exceed wallet max

        // any eth that needs to go back
        uint ethReturn = sub(tokensRequested, tokensToTransfer) / tokensPerETH;
        if (ethReturn > 0) {
            // send it back
            payable(msg.sender).transfer(ethReturn);
        }

        // send eth to treasury and tokens to buyer
        payable(treasury).transfer(sub(msg.value, ethReturn));
        MFI.transferFrom(treasury, msg.sender, tokensToTransfer);
        disbursing = false;
    }

    function min(uint a, uint b, uint c) internal pure returns (uint result) {
        // if a is smallest
        result = a;
        // if be is smaller
        if (result > b) {
            result = b;
        }
        // if c is even smaller
        if (result > c) {
            result = c;
        }
    }

    function sub(uint256 a, uint256 b) internal pure returns (uint256) {
        require(b <= a, "Subtraction underflow" ); 
        uint256 c = a - b; 
        
        return c; 
    } 
    
    // send vested tokens back to treasury 
    function withdrawVested() external { 
        uint timeDiff=block.timestamp - startDate; 
        uint quarter=timeDiff / (90 days); 
        if (quarter> lastVestedQuarter) {
            MFI.transfer(treasury, vestingQuarterly);
            lastVestedQuarter = quarter;
        }
    }
}

interface UniRouter {
    function getAmountsOut(uint amountIn, address[] calldata path)
        external view returns (uint[] memory amounts);
}

/**
* @dev Interface of the ERC20 standard as defined in the EIP.
*/
interface IERC20 {
    /**
    * @dev Returns the amount of tokens in existence.
    */
    function totalSupply() external view returns (uint256);

    /**
    * @dev Returns the amount of tokens owned by `account`.
    */
    function balanceOf(address account) external view returns (uint256);

    /**
    * @dev Moves `amount` tokens from the caller's account to `recipient`.
    *
    * Returns a boolean value indicating whether the operation succeeded.
    *
    * Emits a {Transfer} event.
    */
    function transfer(address recipient, uint256 amount) external returns (bool);

    /**
    * @dev Returns the remaining number of tokens that `spender` will be
    * allowed to spend on behalf of `owner` through {transferFrom}. This is
    * zero by default.
    *
    * This value changes when {approve} or {transferFrom} are called.
    */
    function allowance(address owner, address spender) external view returns (uint256);

    /**
    * @dev Sets `amount` as the allowance of `spender` over the caller's tokens.
    *
    * Returns a boolean value indicating whether the operation succeeded.
    *
    * IMPORTANT: Beware that changing an allowance with this method brings the risk
    * that someone may use both the old and the new allowance by unfortunate
    * transaction ordering. One possible solution to mitigate this race
    * condition is to first reduce the spender's allowance to 0 and set the
    * desired value afterwards:
    * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
    *
    * Emits an {Approval} event.
    */
    function approve(address spender, uint256 amount) external returns (bool);

    /**
    * @dev Moves `amount` tokens from `sender` to `recipient` using the
    * allowance mechanism. `amount` is then deducted from the caller's
    * allowance.
    *
    * Returns a boolean value indicating whether the operation succeeded.
    *
    * Emits a {Transfer} event.
    */
    function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);

    /**
    * @dev Emitted when `value` tokens are moved from one account (`from`) to
    * another (`to`).
    *
    * Note that `value` may be zero.
    */
    event Transfer(address indexed from, address indexed to, uint256 value);

    /**
    * @dev Emitted when the allowance of a `spender` for an `owner` is set by
    * a call to {approve}. `value` is the new allowance.
    */
    event Approval(address indexed owner, address indexed spender, uint256 value);
}</pre>

Let's start with line 7 (L7):

<pre class="h-12 my-10 rounded-md editor" data-linestart="6">...
IERC20 public MFI;
...</pre>

This is telling us that MFI is a public variable of the type IERC20, which is defined in the same file, starting with L110:

<pre class="editor" data-linestart="107">
/**
* @dev Interface of the ERC20 standard as defined in the EIP.
*/
interface IERC20 {
    /**
    * @dev Returns the amount of tokens in existence.
    */
    function totalSupply() external view returns (uint256);
    
    /**
    * @dev Returns the amount of tokens owned by `account`.
    */
    function balanceOf(address account) external view returns (uint256);
    
    /**
    * @dev Moves `amount` tokens from the caller's account to `recipient`.
    *
    * Returns a boolean value indicating whether the operation succeeded.
    *
    * Emits a {Transfer} event.
    */
    function transfer(address recipient, uint256 amount) external returns (bool);
    
    /**
    * @dev Returns the remaining number of tokens that `spender` will be
    * allowed to spend on behalf of `owner` through {transferFrom}. This is
    * zero by default.
    *
    * This value changes when {approve} or {transferFrom} are called.
    */
    function allowance(address owner, address spender) external view returns (uint256);
    
    /**
    * @dev Sets `amount` as the allowance of `spender` over the caller's tokens.
    *
    * Returns a boolean value indicating whether the operation succeeded.
    *
    * IMPORTANT: Beware that changing an allowance with this method brings the risk
    * that someone may use both the old and the new allowance by unfortunate
    * transaction ordering. One possible solution to mitigate this race
    * condition is to first reduce the spender's allowance to 0 and set the
    * desired value afterwards:
    * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
    *
    * Emits an {Approval} event.
    */
    function approve(address spender, uint256 amount) external returns (bool);
    
    /**
    * @dev Moves `amount` tokens from `sender` to `recipient` using the
    * allowance mechanism. `amount` is then deducted from the caller's
    * allowance.
    *
    * Returns a boolean value indicating whether the operation succeeded.
    *
    * Emits a {Transfer} event.
    */
    function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);
    
    /**
    * @dev Emitted when `value` tokens are moved from one account (`from`) to
    * another (`to`).
    *
    * Note that `value` may be zero.
    */
    event Transfer(address indexed from, address indexed to, uint256 value);
    
    /**
    * @dev Emitted when the allowance of a `spender` for an `owner` is set by
    * a call to {approve}. `value` is the new allowance.
    */
    event Approval(address indexed owner, address indexed spender, uint256 value);
}</pre>



## Too much information

In the presale smart contract, we only need the ERC20 functions `balanceOf`, `transferFrom`, and `transfer`, however, the developer included all the functions in the interface IERC20.
            
This is unnecessary.

The irony is that by looking at the other interface defined in the same file:

<pre class="editor" data-linestart="102">
interface UniRouter {
    function getAmountsOut(uint amountIn, address[] calldata path)
    external view returns (uint[] memory amounts);
}</pre>

We can see that here, the developer decided to keep only the required function. This results in a simpler, cleaner, and more efficient code.

Why was that? I don't know, but this is an important check we can add to our development routine:

* Checklist
  * Make sure our interfaces only define the necessary functions;

## Available tokens

The next four lines define two variables used to deal with the amounts of tokens available for the presale:

<pre class="editor" data-linestart="8">    // these aren't ether, we're just using this for unit conversion
    uint public constant presaleSupply = 4_000_000 ether;
    // how much the presale has already issued
    uint public presaleIssued = 0;</pre>

These variables are unsigned ints of 32 bytes (the default size of int and uint) and they are public which means they are visible to outside queriers.

This should be straightforward.

*EDIT:* While reviewing the rest of the contract, I noticed the value of `presaleIssued` is never updated and neither is the `presaleSupply`! Not only could these variables be defined as `constants` or `immutable`, this was probably not intentional as both are used in the sale logic.

Adding to the checklist:

* Checklist
  * Make sure our interfaces only define the necessary functions;</li>
  * (NEW) If you're going to keep "total" variables, make sure you update them.

Moving on. The next line defines the variable where the treasury address will be saved to:`address public treasury;`.
            
If you check the code, this variable is only define once (at construction time), and cannot be changed again.There are other variables like this, in the 3 lines immediately below this, however these have been correctly identified as `constant`.

For some reason this variable was not defined as constant and hardcoded into the code.Let's make sure this does not happen in our smart contracts, by adding this check to our checklist:
            
* Checklist
  * Make sure our interfaces only define the necessary functions;</li>
  * If you're going to keep "total" variables, make sure you update them.
  * (NEW) Define all variables that will not be changed as <code>constant</code> or <code>immutable</code>. <code>constant</code> for variables defined at compile-time and <code>immutable</code> for variables whose value is defined at construction time.

*EDIT*: My friend <a href="https://twitter.com/joaoabrantis">João Abrantes</a> reminded me that the <code>immutable</code> keyword didn't exist in the earlier versions of Solidity. This is true, however it does exist since version 0.6.4 and this contract was compiled using the version 0.6.6.</p>

## Price data, deadcode and flashloans

The following lines define the address contract for USDC, WETH and the Uniswap v2 router.

<pre class="editor" data-linestart="12">...
    address constant USDC = 0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48;
    address constant uniRouter = 0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D;
    address constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;
...
</pre>

Remember that the code also defines an interface for the uniswap router and its function `getAmountsOut`:

<pre class="editor" data-linestart="101">...
    interface UniRouter {
        function getAmountsOut(uint amountIn, address[] calldata path)
        external view returns (uint[] memory amounts);
    }
...
</pre>

Since this is a presale, it's fair to assume that these addresses were going to be used to get the price of ETH against the dollar and to make the presale at a pre-defined dollar cost.

Using the prices from Uniswap directly however should never be done, as it opens your smartcontract to flashloan vunerabilities. Perhaps this is the reason why these variables remain unused. There are no reasons to keep them in the code though.

Which takes us to a few lines bellow:

<pre class="editor" data-linestart="24">...
    // best guess ETH price
    uint public constant dollarsPerETH = 1552;
    uint public constant tokensPerDollar = 4;
    uint public constant tokensPerETH = dollarsPerETH * tokensPerDollar;
...</pre>

As we can see, instead of fixing the price at USD, the project team fixed the price to ETH.

This presale contract was published at Feb-03-2021 (18:06:34 UTC).

At that time ETH was not costing 1552 USD, but 1630 USD (5% more). During the presale time, the price never came down to 1552, however, it reached 1695 USD(Binance) at one point.

That means that the contract was selling MFI for more than 0.27 cents, 2 cents, or 8% over the defined listing price.

Since a presale has the objective of being cheaper than the listing price and knowing all these transactions have associated Ethereum fees, I wouldn't be too happy as an early investor.

Adding to our checklist:

* Checklist
  * Make sure our interfaces only define the necessary functions;</li>
  * If you're going to keep "total" variables, make sure you update them.
  * Define all variables that will not be changed as <code>constant</code> or <code>immutable</code>. <code>constant</code> for variables defined at compile-time and <code>immutable</code> for variables whose value is defined at construction time.
  * (NEW) Deadcode:
     * Remove any unused variables.
     * Remove any unused functions.
     * Remove any unused interfaces and interfaces&#39; functions.
  * (NEW) If you&#39;re going to define prices:
      * Use price oracles to get a current price or define it in ETH.

**EDIT:** turns out Etherscan thinks the price for ETH went as high as 1721 USD, considerably increasing the price error for this presale.

## Vesting

The next part of the contract relates to vesting.

Vesting is a mechanism used in crypto and presales, mainly to give some security to the investors that the people raising the money will not simply sell their tokens and run away from the project, in what is called a rug-pull.

<pre class="editor" data-linestart="16">

    uint public startDate;
    uint public lastVestedQuarter;
    // 1_500_000 / 8
    uint public constant vestingQuarterly = 187_500 ether;
</pre>

In their token distribution article, they have planned an initial distribution of 4'000'000 tokens, with 1'500'000 of these (15%) allocated directly to the team. We can see the first value assigned to the variable `presaleSupply` and the second one being written here as `1_500_000` for the comment justifying how many MFI can be withdrawn each quarter. According to their medium article, the vesting shouldlast 2 years, or 8 quarters.

The function below allows withdrawing the vested tokens:

<pre class="h-56 my-10 rounded-md editor" data-linestart="90">...
    // send vested tokens back to treasury
    function withdrawVested() external {
        uint timeDiff = block.timestamp - startDate;
        uint quarter = timeDiff / (90 days);
        if (quarter > lastVestedQuarter) {
            MFI.transfer(treasury, vestingQuarterly);
            lastVestedQuarter = quarter;
        }
    }
...</pre>

There's a problem with this function. Can you see it? No?

Well, there's a big one.

If don't see it, don't worry. I plan to explain it in part 2 of this article if there's enough interest.

## Part 2?
I hope you enjoyed reading this as much as I enjoyed writing it.

There are still a few things a would like to explore:

* The problem with the vesting function;
* The importance of `get` and `set` functions (thanks [João Andrade](https://twitter.com/jpm_andrade95)!);
* How to get more tokens in the same wallet than you should be allowed to (exploitable nature of contracts like this);
* Some blockchain analysis on the contract:
 * How many tokens were sold?
 * Did everyone get their tokens?
 * Did anyone get more tokens in their wallet than they should?

