## Building a Virtual Lab (Part One)

We all come to the DBA trade from different backgrounds. Some have worked in IT in a different specialty - programmers, network admins, and help desk. But others have a different background altogether: admin assistants, data entry, and other kinds of business users. For those of us from non-IT backgrounds, there are experiences that we may miss. For myself, while I was somewhat adept at building websites, I felt pretty lost when it came to networks and servers.

Of course I've picked up bits here and there, but there are more complete ways to learn these skills. Thanks to the powerful state of computing, I can set up my own virtual environment. And I'd like to write about that experience. Naturally, setting up a networked environment (AKA: lab) will allow for further experimentation with SQL Server as well, such as clustering, mirroring, and replication projects.

## Our Requirements

While building a lab can be time arduous, it isn't overly complicated nor does it have to be expensive. Here's the basic list of requirements:

  * A computer
  * Oracle's VirtualBox
  * Windows Server 2012
  * SQL Server 2012 Developer Edition

I assume you have access to a computer at home or work, and both VirtualBox and Windows Server are free. (This is important for those of us without MVP-access to MSDN for our software needs.) The only item in that list that will cost you money is SQL Server; it's a paltry $40 on Amazon.

However the more money you have to spend - in this case, on your computer - the nicer your lab can become. Since we'll be relying on virtualization, you'll want a beefy host (the computer running VirtualBox) so you can run more virtual machines (guests) concurrently. This is important for our purposes of creating multiple servers in a networked environment.

## Considerations for your Lab

[![Matt's Desktop Computer](../img/photo.jpg)](http://mattvelic.com/wp-content/uploads/2012/11/photo.jpg)

Look ma, cable management!

Here's the specs on my desktop computer. While it may seem impressive, your host doesn't require as much hardware.

  * (64-bit) Windows 7 Pro
  * Intel i5 four-core CPU
  * 32GB of memory
  * Crucial SSD for C: drive
  * 1TB HHD for storage

There are two points to consider for your operating system. First, you should run a 64-bit version so you can access more than 3.5GB of memory. Second, [even the 64-bit version of Windows 7 you choose has an effect on how much memory you can access](http://msdn.microsoft.com/en-us/library/windows/desktop/aa366778(v=vs.85).aspx#physical_memory_limits_windows_7). Home Premium will limit you to 16GB, which should be fine for the purposes of creating a lab, but if you can afford Professional edition (or greater), you won't have to worry about RAM limitations whatsoever.

As for the processor, you'll want a minimum of four cores. If you can afford a processor with hyper-threading, which will double the number of cores presented to your system, that's even better.

The amount of memory is important, and thankfully memory continues to remain inexpensive. I've read examples where people have created sparse environments using a minimum of 8GB of memory, but 12GB to 16GB will give some breathing room to run multiple machines.

As for storage, I am running a combo of an SSD for Windows while everything else is dumped on a large HHD. You don't need to get this complicated: a single hard drive will do fine, you just need the space to create all the virtual hard disks necessary.

Next time, I'll go into the process of creating our first virtual machine through VirtualBox, and then installing Windows Server 2012 onto it.
