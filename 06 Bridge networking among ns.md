# Bridge Networking Among Namespaces

We'll make three separate namespaces called red-ns, blue-ns, and green-ns. Then, we'll connect them using a special network in Linux called a bridge. We'll use virtual cables called veth interfaces. The aim is to let these namespaces communicate with each other, like sending pings.

## What is Bridge Networking

A bridge network is like a virtual switch that connects different parts of a computer network together. It helps devices on the network communicate with each other by forwarding data packets between them. It's commonly used to connect different parts of a network, like connecting computers in a local area network (LAN) or virtual machines in a cloud environment.

In the context of Linux namespaces, a bridge allows virtual network interfaces from different namespaces to communicate with each other as if they were on the same network. By connecting the namespaces through a bridge, data packets can be forwarded between them, enabling communication and interaction among the processes and applications running within each namespace. Essentially, the bridge serves as a virtual link that enables seamless communication across the namespaces.


## Setting up Linux Bridge Network among Namespaces

In this doc, we want to establish a Linux Bridge Network among the 3 namespaces: `red-ns`, `blue-ns`, and `green-ns` and facilitate seamless communication between them.

![alt text](./images/image-Lab6.png)

Setting up a Linux Bridge Network among namespaces involves several steps to create a seamless communication environment. 

Initially, a Linux bridge is created and brought up, serving as the central point of connection. We can use these commands:
```
sudo ip link add dev vnet type bridge
sudo ip link set dev vnet up
```

Each namespace is then established, providing isolated environments for network operations. Virtual Ethernet pairs are generated for each namespace, forming virtual cables to connect them to the bridge. These virtual interfaces are then moved to their respective namespaces, ensuring proper isolation. Subsequently, the interfaces are attached to the bridge, allowing communication between namespaces. We learned how to create ns and virtual ethernet interfaces and configure these interfaces in namespaces and bridge.

All interfaces, including those in namespaces and the bridge, are activated to enable network traffic. Finally, IP addresses are assigned to the virtual interfaces within each namespace, and default routes are set to establish connectivity. To assign IP addresses to the virtual interfaces within each namespace and set the default routes we use the following commands:
```
sudo ip netns exec <namespace-name> ip address add <ip-addr> dev <veth-name>
sudo ip netns exec <namespace-name> ip route add default via <bridge-IP-addr>
```

We need to add the folowing Firewall rules:
```
sudo iptables --append FORWARD --in-interface v-net --jump ACCEPT
sudo iptables --append FORWARD --out-interface v-net --jump ACCEPT
```

These rules enabled traffic to travel across the vnet virtual bridge.These are useful to allow all traffic to pass through the vnet interface without any restrictions.However, keep in mind that using such rules without any filtering can expose your system to potential security risks. Now we can ping from one namespace to other namespaces.



