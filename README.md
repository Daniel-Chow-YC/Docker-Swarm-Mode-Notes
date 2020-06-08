# Docker Swarm Mode

- Cluster
  - Nodes (each node is a VM or physical machine) (can have many nodes in a cluster)
    - Containers (can have many containers in a node)

## Starting a Swarm
- Run ``docker swarm init`` on a node with docker installed
- This node is now the manager of the swarm and is the leader.

## Joining a Swarm
- For another node to join the swarm
- To find the command to run to join the swarm, on a manager run:
  - ``docker swarm join-token worker`` or
  - ``docker swarm join-token manager``
- Run ``docker swarm join --token <crypto_token> <ip>``
- The node joins as a manager node or worker node based upon the token you pass with the --token flag.


## Managers
- Best practice: Have 3, 5 or 7 managers
- Only the ``Leader`` is really active
- Can issue commands for the cluster at any manager but a follower manager just proxies command to the leader manager
- If Leader fails one of the followers becomes leader

## Workers
- Workers do all the application work
- Does not get access to cluster store
- Can be mix of windows or linux

## Lock your swarm with autolock
- Prevents restarted managers from automatically re-joining swarm
- Prevents accidently restoring old copies of the swarm
- Run ``docker swarm update --autolock=true``
- or when you make the swarm ``docker swarm init --autolock``

## Update certificate expiry time
- Run ``docker swarm update --cert-expiry <48h>``
- Each node is given a certificate which lets it be part of swarm.

## Commands
- ``docker node ls`` - list the nodes on the swarm

## Services
- To deploy an application image when Docker Engine is in swarm mode, you create a service. 
- Every service gets a name
  - Names are registered with Swarm DNS
    - Every service uses swarm DNS
- Services can be accessed by name as long your in the same network
- ``docker service create --name <name> -p 8080:8080 --replicas 5 <some_image>``

### Scaling services
- Run ``docker service update --replicas=5 <name_of_service>`` or
- Run ``docker service scale <name_of_service>=5``

### Rolling Updates
- ``docker service update --image <name_of_image>:v2 --update-parallelism 2 --update-delay 10s <name_of_service>``
  - This will update image to version 2
  - ``--update-parallelism`` - Maximum number of tasks updated simultaneously
  - ``--update-delay`` - Delay between updates


## Network Types

### Bridge Network
- Default network
- Containers on a bridge network get their own IP
- Containers on separate bridges are isolated -- can't talk to containers on other bridge networks
- However can use port mappings

### Overlay Networks
- Is a single layer 2 network tat spans multiple hosts (nodes)
- ``docker network create -d overlay <name-of-multihost-network>``
- However is for containers only, ie cant talk to VMs or physical machines

### MACVLAN
- Gives every container its own IP address and MAC address on the existing network
- So containers are visible on existing VLANS
- But requires promiscuous mode -- which cloud providers don't usually allow
- IPVLAN is similar and doesn't require promiscuous mode but it is still experimental
