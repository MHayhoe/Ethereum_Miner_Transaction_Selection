# Ethereum_Miner_Transaction_Selection
Algorithms for cryptocurrency miners to quickly and efficiently select transactions to mine in a block, and for ingesting data from the Ethereum blockchain.

The data used herein was taken from the public Ethereum blockchain as well as the public miner transaction memory pool using <a href="https://github.com/ledgerwatch/erigon">erigon</a>.

For information on Ethereum block packing for mining, especially transaction bundle auctions, see <a href="https://docs.flashbots.net/">FlashBots</a>.

## Loading transactions

For efficiency, the transactions are placed in a single SQLite database for use when selecting the transactions. To make this database:
1. <a href="">Download the data here</a>,
2. Place all CSV files in your working directory,
3. Run all cells in <code>Load_Transactions.ipynb</code>.

## Transaction Packing

The algorithms for selecting the transactions to place into the mined blocks can be found in <code>Transaction_Packing.ipynb</code>. This is an instance of the well-known <a href="https://en.wikipedia.org/wiki/Knapsack_problem">knapsack problem</a>, which is a combinatorial optimization problem. Each block has a fixed capacity, expressed in terms of gas, and each transaction has a maximum gas amount and a fee to be paid for each gas used. The goal of the miner is to pick transactions to place into the block to be mined so they get the highest total revenue, without exceeding the capacity of the block.

We implement two different algorithms:

- The greedy algorithm is very fast and efficient, and is guaranteed to be close to the optimal solution. How close it is guaranteed to get depends on the size of the largest transaction to be considered. If the $n$ available transactions are maintained in a sorted order, the greedy algorithm is $O(n)$.
- The dynamic programming algorithm will guaranteeably produce an optimal solution in $O(nW)$ time for $n$ transaction with a capacity $W$. For mining blocks in Ethereum, typically $W = 30,000,000$, so we approximate the solution by dividing all gas amounts by some factor $k$. This makes the running time of the algorithm $k$ times less, at the expense of optimality due to rounding when dividing the weights by $k$.
