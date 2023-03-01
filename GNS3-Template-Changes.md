

1. Updating VyOS GNS3 Template - **DO THIS PART DURING THE CLASS WORKSHOP!** - Open GNS3 and start a blank project. And take the following actions.

*VyOS Template -> Right Click -> Configure template -> Advanced Tab -> Uncheck "Use as a linked base VM"*

2. Pull a new VyOS out but leave it disconnected, and start the machine. This is THE VyOS template machine. Mistakes here will require significant efforts from Doug to get your VM back!

3. Once started login, go into configuration mode, and run the following commands

```
config
delete interfaces 
set interfaces ethernet eth0 address dhcp
commit
save
exit
poweroff
```
4. Put the template back in linked mode

*VyOS Template -> Right Click -> Configure template -> Advanced Tab -> Check "Use as a linked base VM"*

5. Delete the VyOS object from the project.

6. Pull out a new copy of VyOS and the NAT cloud and connect them together.

7. Boot VyOS. If it crashes the first time, Power it off and try up to two times before asking for help.

8. Login to this new clone of the VyOS template. Verify that it gets an IP address on eth0 from the NAT cloude. Ask for assistance if the machine doesn't get an IP address.

9. Stop then delete this project. The changes made to the template earlier are permanent.