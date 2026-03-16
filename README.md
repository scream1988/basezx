# basezximport time
from web3 import Web3

RPC_URL = "https://mainnet.base.org"

# Contract you want to track
CONTRACT_ADDRESS = Web3.to_checksum_address("0xYOUR_CONTRACT")

def main():
    w3 = Web3(Web3.HTTPProvider(RPC_URL))

    if not w3.is_connected():
        raise RuntimeError("Cannot connect to Base RPC")

    print("Connected to Base")
    print("Tracking new users for contract:", CONTRACT_ADDRESS)

    seen_users = set()
    last_block = w3.eth.block_number

    while True:
        try:
            current_block = w3.eth.block_number

            if current_block > last_block:

                for block_num in range(last_block + 1, current_block + 1):

                    block = w3.eth.get_block(block_num, full_transactions=True)

                    for tx in block.transactions:

                        if tx.to and tx.to.lower() == CONTRACT_ADDRESS.lower():

                            user = tx["from"]

                            if user not in seen_users:
                                seen_users.add(user)

                                print("👤 New User")
                                print("Wallet:", user)
                                print("Block:", block_num)
                                print("Tx:", tx.hash.hex())
                                print()

                last_block = current_block

            time.sleep(2)

        except Exception as e:
            print("Error:", e)
            time.sleep(5)


if __name__ == "__main__":
    main()
