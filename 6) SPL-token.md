Standard ERC 20 implementation of a token is available here:
https://github.com/solana-labs/solana-program-library/tree/master/token/program/src

Alternatively you can use SPL-Token cli which is a very quick approach to creating tokens on Solana.
Here is an exhaustive documentation to the same -> https://spl.solana.com/token. 

Steps to be followed:

1) Install Rust using this command: curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

2) $ cargo install spl-token-cli

3) You can check the current configuration by using: 
$ solana config get

>Config File: ${HOME}/.config/solana/cli/config.yml
>RPC URL: https://api.mainnet-beta.solana.com
>WebSocket URL: wss://api.mainnet-beta.solana.com/ (computed)
>Keypair Path: ${HOME}/.config/solana/id.json

4) If you wish to change the RPC url, you can do so by:
$ solana config set --url https://api.devnet.solana.com

5) If you wish to change the keypair path, you can do so by:
$ solana config set --keypair ${HOME}/new-keypair.json

6) Inorder to fund your wallet use:
$ solana airdrop 1

Alternatively, you can use the following link to get some test Sol coins.
https://solfaucet.com/

7) You can create a Fungible token using:
$ spl-token create-token
Output:
>Creating token EEhcXLnDFAFCdqFBD8LgKzrk495dDtWJchTP137NZFik
>Signature: 5gS9GsYurFDigxz7gEAeABLEZrXNkHmc7FPHq9oq9DLi1WNGViSZChiQZwSsyo3a2kGeanfj32PhHFNrB7SLKPDS

8) Tokens when initially created by spl-token have no supply:

$ spl-token supply EEhcXLnDFAFCdqFBD8LgKzrk495dDtWJchTP137NZFik
>0
9) You need to explicitly create account holders for the token program using the following command:
$ spl-token create-account EEhcXLnDFAFCdqFBD8LgKzrk495dDtWJchTP137NZFik
>Creating account FZzjt1LAizrXkCCgZbUXeMDjaohowTncyXRwMYz34kGv
>Signature: 3M2wUnJormVMR4PzEeZMFC8z6awzQAoMBvxaNLQvwdFyWX7VCh71DxD4STSDtpceZyQ8MphAeKQZqKCVTsJ9BMPH

10) Now you can check supply and balance using spl-token balance/supply <PROGRAM_ADDRESS> respectively
$ spl-token balance EEhcXLnDFAFCdqFBD8LgKzrk495dDtWJchTP137NZFik
>0
$spl-token supply EEhcXLnDFAFCdqFBD8LgKzrk495dDtWJchTP137NZFik
>0

11) Mint using the mint command: 
$ spl-token mint EEhcXLnDFAFCdqFBD8LgKzrk495dDtWJchTP137NZFik 100
>Minting 100 tokens
 > Token: EEhcXLnDFAFCdqFBD8LgKzrk495dDtWJchTP137NZFik
  >Recipient: FZzjt1LAizrXkCCgZbUXeMDjaohowTncyXRwMYz34kGv
>Signature: 374ccw4EK3qdvziKU58QxDPYH8xdyMvRGXZbCmcdHovdyAB7ZKphGYYfhLtQDaPeL3N39meZ86yyGG6uFBsBoH1v

12) Recheck balance and supply

13) You can transfer tokens using transfer
$ spl-token transfer <PROGRAM_ADDRESS> <NUMBER_OF_TOKENS> <ACCOUNT_ADDRESS>

                                            OR

Alternatively you can use Sollet for testing:
Sollet(https://www.sollet.io/ ) allows you to mint test tokens on the Solana Devnet and Testnet directly as a button on the UI.

Please note:

Solana Tokens do not have a name on the chain. There is no way to do that programmatically. You add token metadata by making a pull request here:
https://github.com/solana-labs/token-list/blob/main/src/tokens/solana.tokenlist.json
