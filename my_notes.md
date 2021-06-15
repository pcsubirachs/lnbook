
## Notes
Various use cases derived from examples in this book. 

#### Using LND to spin up nodes

Ensure docker is installed, follow instructions here: https://docs.docker.com/get-docker/

You'll need to run 3 containers simulataneously for this demo to work. 

LND hosts an instance of bitcoind which you can use to interact with.

First, Navigate to lnbook/code/docker.

In a separate terminal, build this docker container with <code> docker build -t lnbook/bitcoind bitcoind </code>

Next, to be able to connect with your future virtual nodes, you'll need to create a LND network.

In another terminal, create the network by executing <code> docker network create lnbook </code>

Ensure you see the "lnbook" network listed by running <code> docker network ls </code>

Now build the LND docker container with <code> docker build -t lnbook/lnd lnd </code>


##### Run the containers

Now, run the bitcoind container. <code> docker run -it --network lnbook --name bitcoind lnbook/bitcoind </code>

This will start mining test blocks, let it run for a few minutes.

Next, run the LND container you built. <code> docker run -it --network lnbook --name lnd lnbook/lnd </code>

The LND container starts up and connects to the bitcoind container over the docker network. First, our LND node will wait for bitcoind to start and then it will wait until bitcoind has mined some bitcoin into its wallet. Finally, as part of the container startup, a script will send an RPC command to the bitcoind node thereby creating a transaction that funds the LND wallet with 10 test BTC.


### Using the LND Command Line Interface or lncli
we can issue commands to our container in another terminal in order to extract information, open channels etc. The command that allows us to issue command-line instructions to the lnd daemon is called lncli. Let’s get the node info using the docker exec command in another terminal window:

<code> docker exec lnd lncli -n regtest getinfo </code>


### Adding nodes to the network
If desired, you can run any combination of LND and c-lightning nodes on the same Lightning network. For example, to run a second LND node you would issue the docker run command with a different container name like so:
<code> docker run -it --network lnbook --name lnd2 lnbook/lnd </code>


## Building a Complete Network