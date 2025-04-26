## Clone of my Own (VM)

Last time, we created our first virtual machine and installed a vanilla copy of Windows Server 2012. And even though it wasn't difficult, I'm sure it took a little bit of time to complete. It would be nice to avoid having to sit through a bunch of installs while rolling out this virtual network, and you can thanks to some wonderful cloning functionality built into VirtualBox.

There are two caveats though: UUIDs and SIDs. [UUIDs are unique identifiers](http://en.wikipedia.org/wiki/Universally_unique_identifier) attached to VMs by VirtualBox. For example, you can't simply copy-and-paste the VDI file: VirtualBox will recognize the copy and stop you from using it, even if you were to delete the original! As for SIDs, those are [security IDs assigned by Windows](http://en.wikipedia.org/wiki/Security_Identifier). Within a network, they should all be unique to each machine, but during the cloning process they are duplicated. In today's installment, we're going to learn how to work around each of these issues.

### Cloning VMs

Cloning a VM is dead simple in VirtualBox. Simply right-click on the Base Install that we created last time, and choose "Clone..."

![Right-click your base VM and choose Clone...](../img/Clone_VM_S1.png)

VirtualBox will ask for a new name. Following my convention, I'm naming mine WS12-Eval-DC-ClusterNode3. (Yeah, I've done this a few times...) You should also check the box to Reinitialize the MAC address.

![Give your clone a new name.](../img/Clone_VM_S2.png)

On the next screen, choose Full Clone since we want these to be completely separate machines at the end of the process. VirtualBox will do it's thing for a few minutes, and you should see your newly cloned machine (with the same Settings) in the list. When you start it up, it will even use the same administrative password as your original.

### Fixing the Windows SID

While cloning in VirtualBox is a painless process, it still leaves you with that duplicate Windows SID. This is an issue for our purposes of creating a network. So now that we've got our clone, we can boot it up and reinitialize the SID so it is different by using sysprep.

Once you've booted up and logged into your cloned machine, open up the PowerShell prompt and type in the following command to open the System Preparation Tool.
    
    C:Windows\System32\Sysprep\sysprep.exe

![Run sysprep.exe to generate a new SID.](../img/Clone_VM_S3.png)

> **This is not recommended for production systems**. It resets configurations on the server and should be done as early as possible in the server setup if used at all. We can get away with it here _because it is our own lab_.

Warning aside, make sure you leave it in Out-of-Box mode, click the Generalize button (which will generate a new SID), and choose the Reboot option.

The system will reset itself, and after restart, you'll be guided through the typical system setup steps that we completed upon first install. But you will have a fresh SID! Next time, we'll set up our domain controller, which acts as the base of our network.
