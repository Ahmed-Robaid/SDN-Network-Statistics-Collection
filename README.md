# Collection of statistics to understand the instantaneous behaviour of the underlying network 

## Problem Definition
The state of the art congestion control algorithms are based on End to End packet statistics. Leveraging the network feedback for congestion control is another promising dimension, but the catch is the availability of network statistics at one place. Software Defined Networking can mitigate
this limitation and hence be a pillar for network suggested TCP Congestion control.

## How does it work? and Versions
A topology created in Mininet having some TCP flows happening between hosts in the topology. User queries a centralised server for getting the needed statstics.
The statistics collection part has 2 versions as mentioned below.

1. StatCollection from all switch interfaces using Python script.
2. Using the statistics collected by ODL. 

### Steps
1. As TCP connection takes place, the sniffer finds the path taken by the connection.
2. In the background statistics are being collected for all interfaces (of all switches).
3. When the user queries for a particular connection,
    1. The list of intermediate switches which were used by the connection is found out.
    2. The statistics for each of these interfaces are extracted and sent back to the client.


## Modules
#### 1. Topology Generation [Source code](https://gitlab.com/IIITB_SDN_2017/MT2016119_MT2016120_StatCollection/blob/master/src/MT2016120_Sabari_Nathan/Generator.py)
* A generic topology generator. 
* Input is a CSV file whose 1st column specifies all nodes
    * Hosts' name should start with _'h'_
    * Switches' name should start with _'s'_
    * Controllers' name should start with _'c'_
* Since the internet can have different controllers controlling different part of network, we have support for different controllers. With a many to one mapping between Switch and controllers.
* Which controller controller controls which switch can also be specified.
* 2nd column onwards specifies the nodes that are neighbours of the node in th e corresponding row's first column.
* Currently the host-switch links have 5Mbps link with a delay of 5ms and the switch to switch links have 100Mbps link with 7.5ms delay.
* Make sure you are in the corresponding directory + followed all [Set-up instructions](https://gitlab.com/IIITB_SDN_2017/MT2016119_MT2016120_StatCollection/blob/master/src/MT2016120_Sabari_Nathan/Setup%20instructions.md)
* Run the script using `sudo python Generator.py`
* Once the script runs, it generates 4 different files  (3 Python scripts and 1 CSV file).
    * topo_consolidated.py
    * stat_consolidated.py
    * sniffer_consolidated.py
    * Switch2PortMapping.csv

#### 2. Sniffer file [Source code](https://gitlab.com/IIITB_SDN_2017/MT2016119_MT2016120_StatCollection/blob/master/src/MT2016120_Sabari_Nathan/snif.py)
* This file is generated by the previous step. 
* Sniffs TCP segments from all the active interfaces of all switches in the Mininet emulation environment.
* Every packet is processed to check if the hextuple (SourceIP - Destination IP - Source TCP Port - Destination TCP Port - SwitchID - Switch's PortID) is unique.
* If unique, it is recorded by sending to MySQL running at an external system. 
* This helps in figuring out the path taken by each TCP flow. 

#### 3. StatCollector - Version 1 [Source code](https://gitlab.com/IIITB_SDN_2017/MT2016119_MT2016120_StatCollection/blob/master/src/MT2016120_Sabari_Nathan/stat_collector.py)
* Internally runs the command `ovs-vsctl get Interface <interface_name> statistics`
* Parses the output and pushes to MySQL server.

#### 4. Web service to query 
*

#### 5. StatCollector - Version 2
*

## Running the project
* `sudo python topo_consolidated.py`
* `xterm <any switch name>`
* `sudo python sniffer_consolidated.py`
* In case you want to use the version 1 of this project, 
    * `xterm <any switch name>`
    * Make sure MySQL server is running and accepting connections
    * In the xterm window run `sudo python stat_consolidated.py`

* In case you want to run the version 2 of this project @maneeshashivakumar to fill this


## Project delegation
1. Statistics collection and SDN-Node communication – Sabari Nathan A. (MT2016120). @sabarinathana
2. Creating web interface between client and SDN controller for information retrieval – Maneesha S. (MT2016119). @maneeshashivakumar