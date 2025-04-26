## Setting up your Virtual Domain Controller

Today we'll be picking up our virtual lab series once again, this time by focusing our efforts on installing a domain controller. It will act as the base for our network, and allow us to join multiple servers to one domain. This is important as it will allow us to experiment with advanced features, such as clustering and availability groups. We'll be up and running in three major steps: cloning and preparing a new virtual machine (VM), installing the appropriate roles to our controller, and configuring and testing our controller.

### Clone me up, Scotty

Before jumping in and cloning a new machine, highlight the VM that holds the vanilla install of Windows Server and enter it's settings. Click on the Network tab. Make sure that Adaptor 1 is enabled (it should be by default), and if need be, change it's "Attached to:" setting from NAT to Internal Network. VirtualBox has some good documentation about all the different Network settings, especially in regards to the kinds of hardware it will virtualize. Simply, we're telling VirtualBox that this server is plugged into a switch on the _intnet_ network.

![Change VirtualBox's network setting.](http://mattvelic.com/?attachment_id=568)

Once you've updated the network setting, follow the directions in the last virtualization post to clone a new VM using your base install. I've called mine DomCon, which stands for Domain Controller. Start it up and run sysprep, per the previous post, to complete the cloning action.

Once the cloning (and reset) process has completed, we can begin the process of changing settings and installing roles for the domain controller. In my walkthrough, you'll have to restart the server twice in order to complete the whole process. The first chunk of work will involve changing setting on the server and installing two roles. The second will come after we've set-up those roles.

### Before the First Restart

Click on the heading, "1. Configure this local server." On the new screen, click on the Computer Name to bring up a dialog box to change it. I've entered "Domain Controller" into the Computer Description box. Then click on the Change… button and change the computer's name to DomCon. Once you click OK and apply the change, you'll get a choice to restart now or later. Choose later, since there is more work we can do before we _absolutely_ need to perform a restart.

![Changing the name of your Domain Controller.](http://mattvelic.com/?attachment_id=569)

After changing the name, click on "IPv4 address assigned…" on the Ethernet setting a few lines down. Right-click on the Ethernet network and choose Properties. In the Ethernet Properties pop-up box, double-click the Internet Protocol Version 4 (TCP/IPv4) connection to open it's properties.

![Open up the Ethernet properties.](http://mattvelic.com/?attachment_id=570)

Inside of the properties, we'll be setting up a static IP address for our domain controller. You can follow the example I've provided in the screenshot, or you can [check out different options](http://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces) as described by Wikipedia. In a nutshell, the IP address should be specific to each machine, and we'll want to route the machine back to itself for DNS purposes by using 127.0.0.1. We can use the default subnet mask, and we can leave the default gateway blank since we've told VirtualBox to think of itself as directly plugged into a network switch. Otherwise, you'd have to supply the IP of the network.

![My DomCon's IP settings.](http://mattvelic.com/?attachment_id=571)

Once you've OK'd and exited out of the network settings, return to the Dashboard (upper left) and click on, "2. Add roles and features." Click through the first page. Choose Role-based or feature-based installation on the Installation Type page, and choose the DomCom server as the target in the Server Selection page. (It should be the only one listed even though the name change hasn't taken effect yet.) On the Server Roles page, click the boxes for Active Directory Domain Services (ADDS) and DNS Server. Be sure to add all the features as requested by the wizard, and don't worry about any warnings that pop-up: you can click through them at this point.

![Adding the ADDS and DNS roles to the server.](http://mattvelic.com/?attachment_id=572)

As for additional Features on the next page, I didn't add anything extra. Click thru to the Confirmation page, click the "Restart the destination server…" box at the top, and finally click the Install button! Once the installation has completed, your server should restart automatically. If not (and it didn't in my case…), simply open up PowerShell and run the "Restart-Computer" command.

### After the Restart

Log back into your machine. If you click on "1. Configure this local server," you'll notice that your changes have been applied. The final bit of work to do is to set up a new domain for your network. In the upper-right hand corner, you'll see a flag with a yellow warning triangle. Click on it, and you should see a post-deployment configuration task waiting for you. Click on "Promote this server to a domain controller."

![Look here to promote the server to DomCon.](http://mattvelic.com/?attachment_id=573)

In the Deployment Configuration, select "Add a new forest" and supply a name for your domain. I've chosen sqllab.local.

![Add a new forest.](http://mattvelic.com/?attachment_id=574)

On the next page, add a password for the Directory Services Restore Mode. The remainder of the options can (more or less) be clicked thru till install. Again, the server should automatically reboot after the operation completes. We've set up our domain controller!

### Testing the Domain

Finally, it's good to test to make sure it's working properly. Simply clone up an addition, new machine, and prep it using sysprep. Again, make sure it's using the Internal Network setting.

Thankfully you won't have to install anything to test the domain. Simply follow the directions above to enter the IPv4 properties (it's where we set up our static IP address). Once there, supply a new IP address, but this time, enter the DomCon's IP address for the Preferred DNS Server (We used 10.0.0.1).

![IP settings for our test machine.](http://mattvelic.com/?attachment_id=575)

Back in the local server properties, click on the Workgroup setting. Click on the Change… button, but instead of changing the computer name, click on the Domain selector in the Member of box. Enter the Administrator's credentials to join the domain. (This is the Admin of the DomCon box, not the local server, if you've been good about using different passwords.) Natually, you'll have to restart after joining the domain.

![Join that domain!](http://mattvelic.com/?attachment_id=576)

And with that we've got the basis for our virtual lab!
