Tutorial Walkthrough
====================

Now that we are familiar with the basic commands, we can begin the tutorial to deploy a contract application and use it on the network.

Clone the Contracts
---------------------

First, we will need to clone `the counter contract repository <https://github.com/casper-ecosystem/counter>`_ to our local machine. 

.. code-block:: bash
    
    git clone https://github.com/casper-ecosystem/counter

If you explore the source code, you will see that there are two smart contracts:

- ``counter-define``
    - Defines two named keys: `counter` to reference the contract and an associated variable `count` to store the number of times we increment the counter
    - Provides a function to get the current count (`counter_get`)
    - Provides a function to increment the current count (`counter_inc`)
- ``counter-call``
    - Retrieves the `counter-define` contract, gets the current count value, increments it, and makes sure count was incremented by 1


Create a Local Network
---------------------------

After you have gotten familiar with the counter source code, we need to create a local blockchain network to deploy the contract. If you completed the NCTL tutorial, all you need to do is allocate the network assets and then start the network.

If you run the following line in your terminal, you should be able to spin up a network effortlessly.

.. code-block:: bash
    
    nctl-assets-setup && nctl-start

.. Note::
    
     If it fails for any reason, please refer the `NCTL tutorial <https://docs.casperlabs.io/en/latest/dapp-dev-guide/setup-nctl.html>`_ and make sure that all your packages are up to date).

View the Network State
---------------------------

With a network up and running, you can use the casper-client query-state command to check the status of the network. However, we first need an `account hash` and the `state-root-hash` so that we can get the current snapshot. Once we have that information, we can check how the network looks.

As a summary, we need to use the following three commands:

1. ``nctl-view-faucet-account``: get the account hash
2. ``casper-client get-state-root-hash``: get the state root hash
3. ``casper-client query-state``: get the network state

Let’s execute the commands in order. First, we need the faucet information:

.. code-block:: bash

    nctl-view-faucet-account

If NCTL is correctly up and running, this command should return quite a bit of information about the faucet account. Feel free to look through the records and make a note of the `account-hash` field and the `secret_key.pem` path because we will often use both.

Next, get the state root hash:

.. code-block:: bash

    casper-client get-state-root-hash --node-address http://localhost:40101

We are using localhost as the node server since the network is running on our local machine. Make a note of the `state-root-hash` that is returned, but keep in mind that this hash value will need to be updated every time we modify the network state. Finally, query the actual state:

.. code-block:: bash

    casper-client query-state \
        --node-address http://localhost:40101 \
        --state-root-hash [STATE_ROOT_HASH] \
        --key [ACCOUNT_HASH]

Substitute the state root hash and account hash values you just retrieved into this command and execute it. Do not be surprised if you see nothing on the network. That is because we have not deployed anything to the network yet!

Deploy the Counter
-----------------------

Let us try deploying the `counter-define` contract to the chain. First, we need to compile it.

The makefile included in the repository makes compilation trivial. With these two commands, we can build (in release mode) and test the contract before deploying it. `make prepare` sets the WASM target and `make test` builds the contracts and verifies them.

.. code-block:: bash

    make prepare 
    make test    

With the compiled contract, we can call the `casper-client put-deploy` command to put the contract on the chain.

.. code-block:: bash

    casper-client put-deploy \
        --node-address http://localhost:40101 \
        --chain-name casper-net-1 \
        --secret-key [PATH_TO_YOUR_KEY]/secret_key.pem \
        --payment-amount 5000000000000 \
        --session-path ./counter/target/wasm32-unknown-unknown/release/counter-define.wasm

- Replace the ``[PATH_TO_YOUR_KEY]`` field with the actual path of where your secret key is stored. It is one of the fields that gets returned when you call `nctl-view-faucet-account`. 
- The `session-path` argument should point to wherever you compiled counter-define.wasm on your computer. The code snippet shows you the default path if the counter folder is in the same directory.

Once you call this command, it will return a deploy hash. You can use this hash to verify that the deploy successfully took place.

.. code-block:: rust

    casper-client get-deploy \
        --node-address http://localhost:40101 [DEPLOY_HASH]

View the Updated Network State
-----------------------------------

Hopefully, the deployment was successful, but is the named key visible on the chain now? Call ``casper-client query-state`` to check it out. 

.. Note::

    We must get the new state root hash since we just wrote a deploy to the chain. 

If you run these two commands, there will be a new counter named key on the chain.

Get the NEW state-root-hash:

.. code-block:: bash

    casper-client get-state-root-hash --node-address http://localhost:40101

Get the network state:

.. code-block:: bash

    casper-client query-state \
        --node-address http://localhost:40101 \
        --state-root-hash [STATE_ROOT_HASH] \
        --key [ACCOUNT_HASH]

We can actually dive further into the data stored on the chain using the query path argument or directly querying the deploy hash. Try the following commands and notice that each one gives you a different level of detail.

Retrieve the specific counter contract details:

.. code-block:: bash

    casper-client query-state --node-address http://localhost:40101 \
        --state-root-hash [STATE_ROOT_HASH] \
        --key [ACCOUNT_HASH] -q "counter"

Retrieve the specific counter variable details:

.. code-block:: bash

    casper-client query-state --node-address http://localhost:40101 \
        --state-root-hash [STATE_ROOT_HASH] \
        --key [ACCOUNT_HASH] -q "counter/count"

Retrieve the specific deploy details:

.. code-block:: bash

    casper-client query-state --node-address http://localhost:40101 \
        --state-root-hash [STATE_ROOT_HASH] --key deploy-[DEPLOY_HASH]

The first two commands access the counter and count named keys, respectively, using the query path argument. The third command uses the deploy hash (the return value of `put-deploy`) to query the state of that specific deploy only.

Increment the Counter
---------------------
We now have a counter on the chain, and we verified everything is good. Now we want to increment it. We can do that by calling the entry-point `counter_inc`, the function we defined in the `counter-define` contract. You can call an entry-point in a deployed contract by using the `put-deploy` command as illustrated here:

.. code-block:: bash
    
    casper-client put-deploy \
        --node-address http://localhost:40101 \
        --chain-name casper-net-1 \
        --secret-key [PATH_TO_YOUR_KEY]/secret_key.pem \
        --payment-amount 5000000000000 \
        --session-name "counter" \
        --session-entry-point "counter_inc"

Notice that this command is nearly identical to the command used to deploy the contract. But, instead of `session-path` pointing to the WASM binary, we have `session-name` and `session-entry-point` identifying the on-chain contract and its associated function to execute. There is no WASM file needed since the contract is already on the blockchain.


View the Updated Network State Again
------------------------------------

After calling the entry-point above, theoretically, the counter value should have been incremented by one, but how can we be sure of that? We can query the network again, but remember that we have to get a new state root hash once again. Let us check if the counter was incremented by just looking at the count with the query argument since we are not concerned with the rest of the chain right now.

Get the NEW state-root-hash:

.. code-block:: bash

    casper-client get-state-root-hash --node-address http://localhost:40101

Get the network state, specifically for the count variable this time:

.. code-block:: bash

    casper-client query-state --node-address http://localhost:40101 \
        --state-root-hash [STATE_ROOT_HASH] \
        --key [ACCOUNT_HASH] -q "counter/count"

You should be able to see the counter variable and observe its value has increased now.

Increment the Counter Again
---------------------------

If you recall, we had a second contract named `counter-call` in the repository. This time around, we can see if we can increment the count using that second contract instead of the session entry-point we used above.

Keep in mind, this is another `put-deploy` call just like when we deployed the `counter_define` contract to the blockchain. The session-path is once again going to be different for you depending on where you compiled the contract.

.. code-block:: bash

    casper-client put-deploy \
        --node-address http://localhost:40101 \
        --chain-name casper-net-1 \
        --secret-key [PATH_TO_YOUR_KEY]/secret_key.pem \
        --payment-amount 5000000000000 \
        --session-path ./counter/target/wasm32-unknown-unknown/release/counter-call.wasm


View the Final Network State
----------------------------

Before we wrap up this guide, let’s make sure that the second contract did in fact, update the counter from the first contract! Just as before, we need a new state-root-hash, and then we can query the network as we have grown accustomed to by now.

Get the NEW state-root-hash:

.. code-block:: bash

    casper-client get-state-root-hash --node-address http://localhost:40101

Get the network state, specifically for the count variable this time:

.. code-block:: bash

    casper-client query-state --node-address http://localhost:40101 \
        --state-root-hash [STATE_ROOT_HASH] 
        --key [ACCOUNT_HASH] -q "counter/count"

If all went according to plan, your counter should have gone from 0 to 1 before and now from 1 to 2 as you incremented it throughout this tutorial. Congratulations on building, deploying, and using a smart contract on your local test network! Now you are ready to build your own dApps and launch them onto the Casper blockchain.
