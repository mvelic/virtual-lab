## Setting up Shared Storage

Welcome back to the virtual lab series! Last time we created a domain controller and joined a machine to a domain. This type of activity is a basic building block to networking computers. Today we're going to look ahead to a more complicated topic: Windows Server clustering. We won't cluster servers today, but we're going to add a vital component that will set us up for next time: shared storage.

### Why share storage?

Clustering two or more computers together provides higher availability to a service through redundancy. Each of these clustered computers is called a Node. In the diagram, only one node is active but should it become unavailable, the cluster can failover to another node to prevent a loss in service. But although each node is a complete computer, they rely on a shared source of storage to manage the data used by the application. Typically, this storage will reside on a SAN.

![Two Servers sharing
Storage](http://commons.wikimedia.org/wiki/File:Typical_dual-node_HA.jpg)

In our case, we need a way to create shared storage. We need to be able to emulate a SAN.

### Fakey SAN

My friend/SQL Server/SAN pro, Joey D'Antoni, told me about a program by StarWind Software that's dead simple for setting up shared storage. It's called StarWind iSCSI SAN Free Edition, and it's what we'll be using to complete this exercise. Even though you have to sign up for an account to download (and you can't use an @gmail.com address…), I can tell you it's completely worth it due to the incredible ease of the program. I can't recommend it enough, because it let me focus on clustering rather than figuring out some other shared storage solution.

While you are logged in and downloading the solution, be sure to pick up the following White Papers and Technical Papers as well. I found them invaluable during this process.

  * Using StarWind with MS Cluster on Windows Server 2008
  * How to Build a SAN Guide
  * How to Configure a Failover Cluster using Microsoft Windows 2003/2008
  * How to Configure a Server Cluster using MS SQL Server 2008

If you download any of them, make sure it's the first one: Using StarWind with MS Cluster on Windows Server 2008. It's going to come in handy shortly.

### Installing StarWind

Installing StarWind couldn't be simpler, but Joey informed me that the program has a bug: it doesn't uninstall properly. You may want to install it on its own VM just in case. If not, you can likely get away installing it on the same machine that is running your domain controller. In my case, I decided to install on its own VM.

Begin by cloning a new VM in VirtualBox. I named mine SharedDrive. Start it up and sysprep it. Once that's finished, you can set it up normally with a new name, a (unique) static IP address, and join it to your sqllab domain. If you don't remember how, just review the earlier posts in this series.

Once complete, you'll want to install a special set of tools called Guest Additions. This allows us to transfer the StarWind program and license key file from the host computer to the VM. You can install it by clicking on the Devices menu item, and selecting Install Guest Additions… The machine will act as though you put a "Guest Additions" CD in the CD-ROM drive, but you can simply follow the installer.

![Installing VirtualBox's Guest Additions](http://mattvelic.com/?attachment_id=593)

Once Guest Additions has been installed, you can go back to your main VirtualBox console and open the Settings for the SharedDrive VM. Click on the Shared Folders tab, and then the folder with the green plus sign in the upper-right hand corner. A pop-up box will ask which directory you'd like to share with the machine. In my case, I shared my Downloads by opening up the File Path, choosing Other, clicking on my user name, and then selecting the Download folder. When you click OK, that location will appear as a Transient Folder. Click Ok again to save the new settings.

![VirtualBox's Shared Folders](http://mattvelic.com/?attachment_id=594)

Inside of your SharedDrive VM, you'll want to open the Explorer and go to the Network. You should see a computer called VBOXSVR (if not, just heed the yellow warning box and turn on sharing). Inside of that should be a folder named VBOXSVRDownloads. And inside is your user's download location. You can now copy the StarWind program and its license key file from your host to your VM. I placed mine directly on the C: drive for ease.

At this point, the installation of StarWind is simple. Double click the program to start and follow the recommended options. Near the end, you'll be asked to enter your license key: open up the file dialog box and point it at the key you copied over moments ago. Once installed, you'll have the basis for creating and sharing storage for your cluster!

### Creating a Shared Storage Volume

I will admit that upon first glance, it isn't immediately obvious what to do to create and share a storage volume. I know I promised ease, and it's there, just bear with me! That documentation I asked you to download earlier, Using StarWind with MS Cluster on Windows Server 2008, is important now since I'll reference it through the remaining portions of the clustering process. It covers an earlier version of the StarWind product, and an earlier version of Windows Server, but the process is the same, so I'll concentrate on the differences for Windows 2012.

In StarWind, right click on the server under the StarWind Servers list. It'll have one of those random, autogenerated names, which is fine. Select Connect. Two more items will appear in a list under the server name: Targets and Devices. To create a new shared volume, you create a new Target and assign a Device to it. If you are following along with the clustering guide, this action is completed on pages 6 through 20 - but it's just a bit different in this new version of StarWind.

![Adding a new StarWind Target](http://mattvelic.com/?attachment_id=589)

Add a new Target by right clicking on the Targets item in the Servers list, and select Add Target. Next, you'll add an alias as per the guide (I called mine SqlData), _but_ you should also click the box to Allow multiple concurrent iSCSI connections (clustering). Then click Next twice and the Finish button to add the target. In the guide, it automatically segued into the Devices dialog, but the latest version of the program doesn't do that anymore.

![Giving your Target an alias](http://mattvelic.com/?attachment_id=590)

To assign the Device to the SqlData target, right click on the newly created target in the Target List and choose Add a New Device to the Target.

![Adding a Device to the Target](http://mattvelic.com/?attachment_id=591)

Adding the device is similar to the steps described in the guide. Choose Virtual Hard Disk, click Next. Choose Image File device, click Next. Choose Create new virtual disk, click Next. On the parameter page, when you have to choose a name and location, if you decide to use an "images" directory (as per the guide), you'll have to create it first or else the program won't be able to create the disk image.

![Adding some Device parameters](http://mattvelic.com/?attachment_id=592)

Add a disk size, and once you've finished with the parameters, you can click Next three more times to accept the default values and finish the device creation. You have a shared drive ready to be included in a cluster!

Next installment, we'll actually delve into setting up a Windows Cluster. And you can bet we'll be relying on that great StarWind guide some more.
