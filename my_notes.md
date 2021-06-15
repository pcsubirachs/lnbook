
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

Download docker-compose here: https://docs.docker.com/compose/install/


### Updates on Linux
---

Update the <code>code/docker docker-compose.yml</code> file to fix bug with c-lightning. Ensure all nodes are running LND for this to work.

Update the <code>setup-channels.sh</code> file as well to be compatible with the LND command line interface or lncli.

<code>
echo Getting node IDs
alice_address=$(docker-compose exec -T Alice bash -c "lncli -n regtest getinfo | jq -r .identity_pubkey")
bob_address=$(docker-compose exec -T Bob bash -c "lncli -n regtest getinfo | jq -r .identity_pubkey")
chan_address=$(docker-compose exec -T Chan bash -c "lncli -n regtest getinfo | jq -r .identity_pubkey")
dina_address=$(docker-compose exec -T Dina bash -c "lncli -n regtest getinfo | jq -r .identity_pubkey")
</code>

---

##### Watch logs from any one container
<code>docker-compose logs -f Alice</code>

### Open channels and route a payment!
<code>cd code/docker</code>
<code>bash setup-channels.sh</code>

A successful payment output should look similar to this:

----

Get 10k sats invoice from Dina
Dina invoice lnbcrt100u1psvjv7tpp5acr39q70h7kr4fgjxe6wz0r2qjqg5jz9mjpp7wptqt6e5nsq723qdqqcqzpgsp5xpqn8m3n664s3zme6n2tl826y47xa77a8682cf68tcpc3h45crpq9qy9qsqcyutex5ynvlykhxvw2rv5uscta7rz9p2kt98qczvs4x8jq3p2muslmlpw53tatzc3tl7cckghfca7r3jjlwec94x3mwfxy07ggx3zscpxvyxzt
Wait for channel establishment - 60 seconds for 6 blocks
Alice pays Dina 10k sats, routed around the network
{
    "payment_hash": "ee071283cfbfac3aa5123674e13c6a04808a4845dc821f382b02f59a4e00f2a2",
    "value": "10000",
    "creation_date": "1623798791",
    "fee": "0",
    "payment_preimage": "0000000000000000000000000000000000000000000000000000000000000000",
    "value_sat": "10000",
    "value_msat": "10000000",
    "payment_request": "lnbcrt100u1psvjv7tpp5acr39q70h7kr4fgjxe6wz0r2qjqg5jz9mjpp7wptqt6e5nsq723qdqqcqzpgsp5xpqn8m3n664s3zme6n2tl826y47xa77a8682cf68tcpc3h45crpq9qy9qsqcyutex5ynvlykhxvw2rv5uscta7rz9p2kt98qczvs4x8jq3p2muslmlpw53tatzc3tl7cckghfca7r3jjlwec94x3mwfxy07ggx3zscpxvyxzt",
    "status": "IN_FLIGHT",
    "fee_sat": "0",
    "fee_msat": "0",
    "creation_time_ns": "1623798791682499710",
    "htlcs": [
    ],
    "payment_index": "3",
    "failure_reason": "FAILURE_REASON_NONE"
}

{
    "payment_hash": "ee071283cfbfac3aa5123674e13c6a04808a4845dc821f382b02f59a4e00f2a2",
    "value": "10000",
    "creation_date": "1623798791",
    "fee": "0",
    "payment_preimage": "0000000000000000000000000000000000000000000000000000000000000000",
    "value_sat": "10000",
    "value_msat": "10000000",
    "payment_request": "lnbcrt100u1psvjv7tpp5acr39q70h7kr4fgjxe6wz0r2qjqg5jz9mjpp7wptqt6e5nsq723qdqqcqzpgsp5xpqn8m3n664s3zme6n2tl826y47xa77a8682cf68tcpc3h45crpq9qy9qsqcyutex5ynvlykhxvw2rv5uscta7rz9p2kt98qczvs4x8jq3p2muslmlpw53tatzc3tl7cckghfca7r3jjlwec94x3mwfxy07ggx3zscpxvyxzt",
    "status": "IN_FLIGHT",
    "fee_sat": "0",
    "fee_msat": "0",
    "creation_time_ns": "1623798791682499710",
    "htlcs": [
        {
            "status": "IN_FLIGHT",
            "route": {
                "total_time_lock": 6751,
                "total_fees": "2",
                "total_amt": "10002",
                "hops": [
                    {
                        "chan_id": "7282065510891520",
                        "chan_capacity": "1000000",
                        "amt_to_forward": "10001",
                        "fee": "1",
                        "expiry": 6711,
                        "amt_to_forward_msat": "10001010",
                        "fee_msat": "1010",
                        "pub_key": "03c84800871e47bc098c3b17ef0d2f0170efaaa30f0479bea4f3ce6bed913d6fc6",
                        "tlv_payload": true,
                        "mpp_record": null,
                        "custom_records": {
                        }
                    },
                    {
                        "chan_id": "152832116391936",
                        "chan_capacity": "1000000",
                        "amt_to_forward": "10000",
                        "fee": "1",
                        "expiry": 6671,
                        "amt_to_forward_msat": "10000000",
                        "fee_msat": "1010",
                        "pub_key": "02488c4da21628f1e999eaacde5bb36d54742ac1019ed16dacb888c2d6c8c54b14",
                        "tlv_payload": true,
                        "mpp_record": null,
                        "custom_records": {
                        }
                    },
                    {
                        "chan_id": "152832116457472",
                        "chan_capacity": "1000000",
                        "amt_to_forward": "10000",
                        "fee": "0",
                        "expiry": 6671,
                        "amt_to_forward_msat": "10000000",
                        "fee_msat": "0",
                        "pub_key": "03b345b3dfff78a62de9c9b2c575057e078e126a4bd0d1fb05efcf22827e743b9d",
                        "tlv_payload": true,
                        "mpp_record": {
                            "payment_addr": "304133ee33d6ab088b79d4d4bf9d5a257c6efbdd3e8eac27475e0388deb4c0c2",
                            "total_amt_msat": "10000000"
                        },
                        "custom_records": {
                        }
                    }
                ],
                "total_fees_msat": "2020",
                "total_amt_msat": "10002020"
            },
            "attempt_time_ns": "1623798791706588828",
            "resolve_time_ns": "0",
            "failure": null,
            "preimage": null
        }
    ],
    "payment_index": "3",
    "failure_reason": "FAILURE_REASON_NONE"
}

{
    "payment_hash": "ee071283cfbfac3aa5123674e13c6a04808a4845dc821f382b02f59a4e00f2a2",
    "value": "10000",
    "creation_date": "1623798791",
    "fee": "2",
    "payment_preimage": "5d00b3ac5ef9eff08bc03d4e636c819833ee521e42de5089e8d6ab63d45848ff",
    "value_sat": "10000",
    "value_msat": "10000000",
    "payment_request": "lnbcrt100u1psvjv7tpp5acr39q70h7kr4fgjxe6wz0r2qjqg5jz9mjpp7wptqt6e5nsq723qdqqcqzpgsp5xpqn8m3n664s3zme6n2tl826y47xa77a8682cf68tcpc3h45crpq9qy9qsqcyutex5ynvlykhxvw2rv5uscta7rz9p2kt98qczvs4x8jq3p2muslmlpw53tatzc3tl7cckghfca7r3jjlwec94x3mwfxy07ggx3zscpxvyxzt",
    "status": "SUCCEEDED",
    "fee_sat": "2",
    "fee_msat": "2020",
    "creation_time_ns": "1623798791682499710",
    "htlcs": [
        {
            "status": "SUCCEEDED",
            "route": {
                "total_time_lock": 6751,
                "total_fees": "2",
                "total_amt": "10002",
                "hops": [
                    {
                        "chan_id": "7282065510891520",
                        "chan_capacity": "1000000",
                        "amt_to_forward": "10001",
                        "fee": "1",
                        "expiry": 6711,
                        "amt_to_forward_msat": "10001010",
                        "fee_msat": "1010",
                        "pub_key": "03c84800871e47bc098c3b17ef0d2f0170efaaa30f0479bea4f3ce6bed913d6fc6",
                        "tlv_payload": true,
                        "mpp_record": null,
                        "custom_records": {
                        }
                    },
                    {
                        "chan_id": "152832116391936",
                        "chan_capacity": "1000000",
                        "amt_to_forward": "10000",
                        "fee": "1",
                        "expiry": 6671,
                        "amt_to_forward_msat": "10000000",
                        "fee_msat": "1010",
                        "pub_key": "02488c4da21628f1e999eaacde5bb36d54742ac1019ed16dacb888c2d6c8c54b14",
                        "tlv_payload": true,
                        "mpp_record": null,
                        "custom_records": {
                        }
                    },
                    {
                        "chan_id": "152832116457472",
                        "chan_capacity": "1000000",
                        "amt_to_forward": "10000",
                        "fee": "0",
                        "expiry": 6671,
                        "amt_to_forward_msat": "10000000",
                        "fee_msat": "0",
                        "pub_key": "03b345b3dfff78a62de9c9b2c575057e078e126a4bd0d1fb05efcf22827e743b9d",
                        "tlv_payload": true,
                        "mpp_record": {
                            "payment_addr": "304133ee33d6ab088b79d4d4bf9d5a257c6efbdd3e8eac27475e0388deb4c0c2",
                            "total_amt_msat": "10000000"
                        },
                        "custom_records": {
                        }
                    }
                ],
                "total_fees_msat": "2020",
                "total_amt_msat": "10002020"
            },
            "attempt_time_ns": "1623798791706588828",
            "resolve_time_ns": "1623798792245314003",
            "failure": null,
            "preimage": "5d00b3ac5ef9eff08bc03d4e636c819833ee521e42de5089e8d6ab63d45848ff"
        }
    ],
    "payment_index": "3",
    "failure_reason": "FAILURE_REASON_NONE"
}


----

If this doesn't work immediately, wait 5 to 10 minutes for more blocks to be mined. 

That's it! You've now connected multiple nodes and executed a script that allowed Alice to pay Dina 10,000 sats instantly.


## Extra Challenges