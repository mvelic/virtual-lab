## Building a Windows 2012 Cluster

Last time, we learned how to create a volume of shared storage, which is a necessary component to creating a cluster. Today, we're going to create that cluster. As a reminder, a cluster is a grouping of two or more computers (nodes) that are used to provide high availability to a resource, such as an application or a database. In the diagram below, the cluster is represented by the two servers at the bottom.

![Two Servers sharing Storage](http://commons.wikimedia.org/wiki/File:Typical_dual-node_HA.jpg)

So let's get to it!

### Cluster Ingredients

I like to think of this project a bit like a recipe. In order to cook up a cluster, we're going to need the following ingredients:

  * Domain Controller
  * Shared Storage
  * Two basic Windows Servers

If you've been working along with this series, you've already got most (if not all) of the pieces required! If not, take the time to go back and review what we've done thus far. Each of those posts describe what you'll need to do to prepare - today's post is going to concentrate on the few odd details we need to modify in order to get the cluster working, and then we'll piece together all the machines and create our cluster.

### Heartbeats

One important aspect of clustering is that it uses more than a single network connection. In previous posts, we've been working with one network connection - VirtualBox's default _intnet_ Internal Network. VBox's Internal Network acts as if you've simply plugged in a bunch of machines into the same physical switch. While this is good for basic networking examples, clustering needs a second network - a heartbeat network.

Think of it this way: a company's typical ethernet network is like public transportation. Everyone uses it for all manner of things: connecting to the Internet, working with files, sending email. Just like public transportation, people can do things that can cause blockages and slowdowns in the system. For example, someone may think, "Hey, I need this file, and it's only 10 gigs… I'll just copy it to my computer." All of a sudden, the network isn't working so well. There's a traffic jam as a whole lot of information is being sucked through too small a connection.

This is where the heartbeat network comes in. It's running on a different network that can only be seen by the computers in the cluster. It's used to confirm the status of each node and other communication. But because it's on a private network, it isn't affected by the normal traffic occurring on the public network. Even though a traffic jam may be happening, the nodes in the cluster can still communicate without interruption.

For our purposes, we'll be adding a second network switch to all of our machines. It will be running on a different set of IP addresses completely separate from those we've been using thus far.

In VirtualBox, you'll want to open up the Settings for each of the machines you'll be using. In the Network tab, you'll want to click on Adapter2, enable it, and then set up a second Internal Network. I've named mine "heartbeat."

![Adding a second network adapter.](http://mattvelic.com/virtual-lab-p6/vbox_heartbeat_network/)

Inside of each VM, when you check the Network Connections, you'll see that there is now an Ethernet2, which is the heartbeat adapter we added moment ago. Double click on it, click the Properties button, and then double click on the IPv4 option in the connection list. You'll want to define a static IP for each machine in the heartbeat network. For ease, I've included the settings for each of my machines in the following table.

**intnet**
**heartbeat**

**Domain Controller**
IP Address

10.0.0.1

196.168.0.1

Subnet Mask

255.255.255.0

255.255.255.0

Default Gateway

DNS Server

127.0.0.1

127.0.0.1

**Node1**
IP Address

10.0.0.2

196.168.0.2

Subnet Mask

255.255.255.0

255.255.255.0

Default Gateway

DNS Server

10.0.0.1

196.168.0.1

**Node2**
IP Address

10.0.0.3

196.168.0.3

Subnet Mask

255.255.255.0

255.255.255.0

Default Gateway

DNS Server

10.0.0.1

196.168.0.1

**Shared Drive**
IP Address

10.0.0.20

Subnet Mask

255.255.255.0

Default Gateway

DNS Server

10.0.0.1

You'll notice that each network, intnet versus heartbeat, is running on a different set of IP addresses - but that we can use our domain controller as a basis for both of them. The other detail you'll note is that our shared storage doesn't require a heartbeat IP. This is because while the shared storage drive is needed for a cluster, it isn't really a part of the machines that are being clustered. You'll see this more clearly later on.

_Warning:_ if you get an error while attempting to join a node to your domain controller - something along the lines of "It cannot be found" - VirtualBox is at fault. While it may seem to be common sense to think that Adapter1 and Adapter2 would equate to Ethernet and Ethernet2 respectively… it doesn't have to. You can test it out by turning off one of the Adapters in the Networking settings (to see which is which), but you can swap the IP settings inside the machine as well.

### Cluster Up

Once you've joined all the machines to the domain, you're ready to begin the process of clustering them. At this point, we're going to be referring to StarWind's wonderful guide that I linked to last time, Using StarWind with MS Cluster on Windows Server 2008. Pages 21 through 45 describe the process (with a lot of images!) of using the iSCSI Initiator to discover the shared storage volume and then using Disk Management to partition it. Start in Node1 by clicking on the Tools button in the top-left corner, then choosing the iSCSI Initiator. You'll need to turn it on. Other than that, Windows 2012 follows the guide closely. Once you've connected it, you can get to the Disk Management by clicking on that same Tools button and choosing Computer Management.

When you repeat the process on Node2, you'll only need to bring the disk online in the Computer Manager since it's already been partitioned. (Side note: the guide goes through two shared volumes. We're not going to; I'm just using the same SqlData volume I created last time.) You do want to make sure that the shared volume is using the same drive letter on both nodes.

Once the shared storage has been connected and partitioned on both nodes, return to Node1 to install the Failover Clustering Feature (in the guide, this occurs over pages 46 through 49). Back on the main page, you'll want to click on option 2, "Add roles and features." Once the wizard starts however, you can click Next until you get to the Features tab. That's where Failover Clustering is (rather than attached to a Role). You'll want to install the additional add-ons as well. Click Next, and then Install. You can restart the server and repeat the process on Node2.

![Adding the Failover Cluster feature.](http://mattvelic.com/virtual-lab-p6/add_clustering/)

Once both nodes have restarted, we're going to test the clustering settings. Should they pass, we can immediately cluster the two nodes. If not, we'll have to do some troubleshooting. (This is described in the guide over pages 68 to 74.)

With both nodes running, enter Node1 **but use your domain login**. This is the admin account that we created on the domain controller. In my case, the login is SQLLAB\Administrator. You can use the little back arrow in the login screen to Switch Users. This is necessary to test and install the cluster.

Once logged in, click on the Tools button, and choose Failover Cluster Manager. Click on Validate Configuration. Follow the wizard by adding your nodes, and choosing to Run all tests. Assuming all returned well (and you may see some non-critical errors), you're ready to cluster those nodes. If not, leave me a comment and I'll see if I can't help troubleshoot your issues.

When you click Finish, you should immediately jump into the Create a Cluster wizard. (Creating the cluster is described over pages 75 to 81 in the guide.) Click Next, give your cluster a name and complete the IP addresses for it, taking care to not use any that you've used before. Click next two more times to validate and create the cluster!

![Define your cluster access point.](http://mattvelic.com/virtual-lab-p6/cluster_access_point/)

You have clustered! This wraps up a major point in this series. Next time, we'll pick up with some SQL Server topics, beginning with clustering SQL Server.
