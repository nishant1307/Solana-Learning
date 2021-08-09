Developing the bridge
This document is a replica of the following official Readme.
https://github.com/certusone/wormhole/blob/main/DEVELOP.md

Local Devnet
The following dependencies are required for local development:

Go >= 1.15.6
Docker / moby-engine >= 19.03
Tilt >= 0.20.8
NodeJS/npm >= 14
Any of the local Kubernetes clusters supported by Tilt. We recommend minikube >= v1.21.0 with the kvm2 driver.
See the Tilt docs docs on how to set up your local cluster - it won't take more than a few minutes to set up! Example minikube invocation, adjust limits as needed:

minikube start --cpus=8 --memory=8G --disk-size=50G --driver=kvm2
npm wants to set up an insane number of inotify watches in the web container which may exceed kernel limits. The minikube default is too low, adjust it like this:

minikube ssh 'echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p'
This should work on Linux, MacOS and possibly even Windows.

By default, the devnet is deployed to the wormhole namespace rather than default. This makes it easy to clean up the entire deployment by simply removing the namespace, which isn't possible with default. Change your default namespace to avoid having to specify -n wormhole for all commands:

kubectl config set-context --current --namespace=wormhole
After installing all dependencies, just run tilt up. Whenever you modify a file, the devnet is automatically rebuilt and a rolling update is done.

Launch the devnet while specifying the number of guardians nodes to run (default is five):

tilt up -- --num=1
If you want to work on non-consensus parts of the code, running with a single guardian is easiest since you won't have to wait for k8s to restart all pods.

Usage
Watch pod status in your cluster:

kubectl get pod -A -w
Get logs for single guardian node:

kubectl logs guardian-0
Restart a specific pod:

kubectl delete pod guardian-0
Generate test Ethereum -> Solana transfers once the cluster is up:

scripts/send-eth-lockups.sh
Generate test Solana -> Ethereum transfers:

scripts/send-solana-lockups.sh
Run end-to-end tests:

cd bridge
go test github.com/certusone/wormhole/bridge/e2e
Adjust number of nodes in running cluster: (this is only useful if you want to test scenarios where the number of nodes diverges from the guardian set - otherwise, tilt down --delete-namespaces and restart the cluster)

tilt args -- --num=2
Tear down cluster:

tilt down --delete-namespaces
Once you're done, press Ctrl-C. Run tilt down to tear down the devnet.

Web UI
The deployment includes a web UI that uses MetaMask to demonstrate token transfers. It's experimental and meant as example code on how to interact with Wormhole tokens - the UI itself is just the bare minimum to demonstrate how to use the libraries. The Tilt deployment automatically sets up port forwardings on your local machine so you can access the devnet and the UI.

To access the UI, install the MetaMask browser extension and add a custom network with RPC URL http://localhost:8545 and chain ID 0x539. Import the hardcoded Ganache seed phrase as account:

myth like bonus scare over problem client lizard pioneer submit female collect
You can now play with the web UI by initiating token transfers in either directions. The devnet comes with a number of deterministic accounts on both chains that you can use (see below for copy&paste).

For example, send a bunch of SPL tokens to Ethereum:

You can now add the wrapped token address - <TOKEN_ADDRESS> - as a custom token to MetaMask and you'll see your 1000 brand new wrapped tokens:


Next, send some of them back to Ethereum:


MetaMask will ask you to confirm the transaction.

After a few seconds, the SPL token balance will increase as the VAA gets accepted on Solana.

Devnet addresses
Solana

Account	Description
6sbzC1eH4FTujJXWj51eQe25cYvr4xfXbJ1vAj7j2k5J	id.json account in the setup container [1]
Bridge1p5gheXUvJ6jGWGeCsgPKgnE3YgdGKRVCMY9o	Bridge contract
TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA	SPL token contract
6qRhs8oAuZYLd4zzaNnQHqdRyknrQQWDWQhALEN8UA7M	Example SPL token
3C3m4tjTy4nSMkkYdqCDSiCWEgpDa6whvprvABdFGBiW	Account that holds 6qRhs8oA... SPL tokens
85kW19uNvETzH43p3AfpyqPaQS5rWouq4x9rGiKUvihf	Wrapped token for the 0xCfEB86... ERC20 token
7EFk3VrWeb29SWJPQs5cUyqcY3fQd33S9gELkGybRzeu	Account that holds 85kW19u... wrapped tokens [2]
9ESkHLgJH4zqbG7fvhpC9u2ZeHMoLJznCHtaRLviEVRh	Wrapped token for the terra18vd8f... CW20 token
EERzaqe8Agm8p1ZkGQFq9zKpP7MDW29FX1pC1vEw9Yfv	Account that holds 9ESkHLg... wrapped tokens
[1]: The account will eventually run out of funds if you run the lockup sending scripts for a long time. Refill it using kubectl exec solana-devnet-0 -c setup -- cli airdrop solana-devnet:9900 (see devnet_setup.sh).

[2]: This is where tokens sent by scripts/send-eth-lockups.sh end up.

Ethereum

Account	Description
0x90F8bf6A479f320ead074411a4B0e7944Ea8c9C1	Ganache main account (w/ the seed phrase above)
0x5b1869D9A4C187F2EAa108f3062412ecf0526b24	Bridge contract
0xe78A0F7E598Cc8b0Bb87894B0F60dD2a88d6a8Ab	Wrapped asset contract
0xCfEB869F69431e42cdB54A4F4f105C19C080A601	Example ERC20 token
0xf5b1d8fab1054b9cf7db274126972f97f9d42a11	Wrapped asset address for the 6qRhs8oA... SPL token