## Merge_point

As we all know, the amount of data on the chain will continue to increase with the increase of block height, which will occupy more and more disk space. In addition, it takes a long time for new full-node users to synchronize all blocks from the genesis block (block height is 0). The **merge_point** feature allows you to start with a specified block height on the chain and synchronize only the blocks that follow it, thus significantly reducing synchronization time and saving a lot of disk space.





## How to use

You should first understand the askcoin's full-node configuration file, as follows:

[***Askcoin Configuration***](https://github.com/lichuan/askcoin#configuration)

Let's assume that the latest block height of askcoin has reached 250,000, and now you want to reduce disk usage through merge_point, but how do you choose the **merge_point**? One reason for choosing which block to use as a **merge_point** is that it has been widely accepted by all nodes, so suppose that the block height 200,000 as the merge_point, now we can modify the ***config.json*** file to use merge_point:

1. stop the full node you are currently running, add a **merge_point** field in your ***config.json*** and fill in the **export_path**, **block_id**, **block_hash **of the merge_point:

```json
{
    "log_level": "info",
    "log_path": "./log",
    "db_path": "./db",
    "network": {
        "p2p": {
            "host": "here should be your host (domain or ip address)",
            "port": 18050,
            "max_conn": 1000,
            "init_peer": [
                {
                    "host": "node1.askcoin.me",
                    "port": 18050
                },
                {
                    "host": "node2.askcoin.me",
                    "port": 18050
                }
            ]
        },
        "websocket": {
            "enable": true,
            "host": "0.0.0.0",
            "port": 19050,
            "max_conn": 5000
        }
    },
    "merge_point": {
        "export": {
            "block_id": 200000,
            "block_hash": "AAB7D8a5cRxMUw/8MInehnxyQMswNnwzAldfW53GtdM=",
            "export_path": "./merge_point/merge_point_200000.json"
        }
    }
}
```

 	2. restart your full-node and wait for it to export the merge_point file to **export_path**, the full-node will stop running after exporting the merge_point file
 	3. In the merge_point field structure, delete **export** field, add **import** field and fill in **block_id**, **block_hash**, **import_path** fields and modify **db_path** field to a new directory:

```json
{
    "log_level": "info",
    "log_path": "./log",
    "db_path": "./db_new_200000",
    "network": {
        "p2p": {
            "host": "here should be your host (domain or ip address)",
            "port": 18050,
            "max_conn": 1000,
            "init_peer": [
                {
                    "host": "node1.askcoin.me",
                    "port": 18050
                },
                {
                    "host": "node2.askcoin.me",
                    "port": 18050
                }
            ]
        },
        "websocket": {
            "enable": true,
            "host": "0.0.0.0",
            "port": 19050,
            "max_conn": 5000
        }
    },
    "merge_point": {
        "import": {
            "block_id": 200000,
            "block_hash": "AAB7D8a5cRxMUw/8MInehnxyQMswNnwzAldfW53GtdM=",
            "import_path": "./merge_point/merge_point_200000.json"
        }
    }
}
```

4. Restart your full-node, now the full-node will use block height 200,000 as a new starting point. From that starting point, it will synchronize the remaining blocks from other nodes and save them to the new db directory (**db_new_200000 **directory). Because the block history before block 200,000 has been merged into a single merge_point file, it saves a lot of disk space. When synchronization is complete, you can safely delete the old **db** directory (but If you have enough disk space, I suggest you backup the **old db** directory as a historical proof)





## Summary

Through the **merge_point** mechanism, it can greatly reduce the occupancy of disk space, but also significantly reduce the synchronization time of new users of full-node. The core of the blockchain is consensus, so it can be predicted that as long as the communication protocols between nodes remain unchanged or compatible, the **merge_point** mechanism can run perfectly, and the **askcoin network** can continue to exist for many years.

