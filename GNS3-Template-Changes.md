## GNS3 Updating Template VMs

If you are here you have either clicked on something that you shouldn't have or you have need to change the BASE functionality of a GNS3 VM. This is dangrous stuff. If you screw it up it the fix may require wiping the entire gHost machine back to a "like-new" state (i.e. you'll loose any data saved on the gHost machine).

1. Open GNS3 and start a blank project. Go to the left side of GNS3 here all the templates are listed. DO NOT pull one out (yet). On the VyOS icon in the left side list perform the following actions.

*Right Click -> Configure template -> Advanced Tab -> **Uncheck** "Use as a linked base VM"*

2. Any VyOS VMs made at those point will change the BASE functionality of the VM (i.e. NOT a linked clone as usual). Pull out a new VyOS out but leave it disconnected, start the machine. 

3. Once booted up completely login and run the following commands:

```
config
del interfaces 
set interfaces ethernet eth0 address dhcp
commit
save
exit
poweroff
```
4. To put the VyOS template back in linked mode. On the VyOS icon in the left side list perform the following actions.

*Right Click -> Configure template -> Advanced Tab -> **Check** "Use as a linked base VM"*

5. Delete the VyOS object from the project.

6. Pull out a new copy of VyOS and the NAT cloud and connect them together. This new one should be a "normal" GNS3 VyOS object (i.e. [ephemeral](https://www.merriam-webster.com/dictionary/ephemeral)).

7. Boot VyOS object. If it crashes the first time, power it off and try a few times before asking for help.

8. Login to this new clone of the VyOS template. Verify that it gets an IP address on eth0 from the NAT cloude. Ask for assistance if the machine doesn't get an IP address.

9. Stop then delete this project. The changes made to the template earlier are now permanent (i.e. [non-emphemeral](https://en.wiktionary.org/wiki/nonephemeral)).