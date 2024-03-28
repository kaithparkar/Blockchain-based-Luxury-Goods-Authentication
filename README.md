# Blockchain-based-Luxury-Goods-Authentication
Use blockchain technology to create a tamper-proof system for authenticating luxury goods, combating counterfeiting and fraud.
import hashlib
import time
from typing import List

class Block:
    def __init__(self, index, transactions, timestamp, previous_hash):
        self.index = index
        self.transactions = transactions
        self.timestamp = timestamp
        self.previous_hash = previous_hash
        self.nonce = 0
        self.hash = self.calculate_hash()

    def calculate_hash(self):
        block_string = "{}{}{}{}{}".format(self.index, self.transactions, self.timestamp, self.previous_hash, self.nonce)
        return hashlib.sha256(block_string.encode()).hexdigest()

    def mine_block(self, difficulty):
        while self.hash[:difficulty] != '0' * difficulty:
            self.nonce += 1
            self.hash = self.calculate_hash()

class Blockchain:
    def __init__(self, difficulty=4):
        self.unconfirmed_transactions = []
        self.chain = []
        self.create_genesis_block()
        self.difficulty = difficulty

    def create_genesis_block(self):
        genesis_block = Block(0, [], time.time(), "0")
        genesis_block.mine_block(self.difficulty)
        self.chain.append(genesis_block)

    def add_new_transaction(self, transaction):
        self.unconfirmed_transactions.append(transaction)

    def add_block(self, block):
        previous_hash = self.chain[-1].hash
        block.previous_hash = previous_hash
        block.mine_block(self.difficulty)
        self.chain.append(block)

    def is_chain_valid(self):
        for i in range(1, len(self.chain)):
            current = self.chain[i]
            previous = self.chain[i-1]

            if current.hash != current.calculate_hash():
                return False
            if current.previous_hash != previous.hash:
                return False
        return True

# Example usage:
luxury_blockchain = Blockchain()

# Adding a new luxury item transaction
luxury_blockchain.add_new_transaction("Luxury Watch #1234 - Authentic")
luxury_blockchain.add_block(Block(luxury_blockchain.chain[-1].index + 1, luxury_blockchain.unconfirmed_transactions, time.time(), luxury_blockchain.chain[-1].hash))
luxury_blockchain.unconfirmed_transactions = []  # Clearing the list of transactions after adding them to the blockchain

print(f"Blockchain is valid: {luxury_blockchain.is_chain_valid()}")

# Outputting the blockchain
for block in luxury_blockchain.chain:
    print(f"Block {block.index}: {block.transactions} - Hash: {block.hash}")
