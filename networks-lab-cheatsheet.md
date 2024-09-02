# Commands for networks and switching (this is incomplete send a pull request if you want to add/fix stuff)
Link to github version: [https://github.com/shrimp332/tne10006-lab-notes](https://github.com/shrimp332/tne10006-lab-notes)  
## Keys:
You don't have to type any of these it's just to make clear what type of command it is.
- `>` User mode (Default)
- `#` Admin mode (use `enable` to get into this mode)
- `(config)#` Config mode (use `conf` when in admin mode)
- `$` This is not a cisco prompt but a command you can use in cmd, powershell or bash on the lab computers

## How to clean up after you're done (Important)
- `# delete vlan.dat` this will remove all the vlans as they are persistant
- `# write erase` this will clear the running config
you can then log out and turn off the switches

if you need to restart for some reason, do the above steps then run
- `reload`

## Useful tips
Always do:  
- `# conf line console 0`  
- `(config)# logging synch`  
This will make the logs synchronous with your user input.
Eg.
If you're halfway through typing a command when a log is printed it will get mixed with what you're writing.
this command will ensure that what you're writing and the logs are on different lines

Another tip is when you're in `(config)#` mode you can use `do` to run an admin mode command
Eg.
- `(config)# do sh ip int brief` which will show ip interfaces even though you're in config mode

Tab will auto complete the command you're writing on cisco machines. You can also write short hand eg `sh ip int bri` instead of `show ip interface brief`

If you type in the wrong command the switch will try to do a dns lookup, this will fail because it is not connected to the internet.
- `(config)# no ip domain-lookup`
will disable this, making life easier.

## Test connectivity
- `>ping <addr>`
This will ping a network address.
these switches and routers aren't able to connect to the internet so they can only ping eachother and the lab VAN and ethernet virtual machines
- ! mean success
- . means failed to connect

## Interfaces
Show interfaces:  
- `>sh ip int brief`

### Shutdown interface
- `(config)# int giga 1/0/1`
- `(config-if)# shutdown` use `no shutdown` to start it

### Shutdown range
You can use this to disable unused ports, something that you have to do for some practicals
- `(config)# int range giga 1/0/1 - 4` most operations can be done on a range of interfaces at once
- `(config-if)# shutdown`

## VLAN
### Setting up a vlan
- `(config)# vlan <number>` eg vlan1
- `(config-vlan)# name <name>`

### Disable Vlan (not 100% sure on this double check with the lab notes)
- `(config)# vlan <number>`
- `(config-vlan)# shutdown`
- `(config)# int vlan <number>`
- `(config-if)# shutdown`

### Assign interfaces to vlan 1
- `(config)# int giga 1/0/1`
- `(config-if)# switchport mode access` (it might be switch port instead of switchport, i don't remember)
- `(config-if)# switchport access vlan <number>`

### Set ip address on vlan
- `(config)# int vlan1` will select interface vlan1. interface vlan1 is the virtual interface that is connected to vlan1 (i think)
- `(config-if)# ip addr <addr> <subnet mask>` eg. ip addr 192.168.1.2 255.255.255.0

## Trunking
Trunking allows multiple vlans to communicate over the same wire/interface

### Set interface to trunk
- `(config)# int <interface>`
- `(config-if)# switchport mode trunk`

## Port sec
port sec is port security stuff, i'm not 100% sure this stuff is right, check lab handout
- `>sh port-sec int` (idk if this is right lol test it)
Note i'm not sure if it's `portsec` or `port-sec`

### set portsec up
- `(config-if)# switchport port-sec` enables port-sec
- `(config-if)# switchport port-sec max 2` I believe this sets it so the switch only registers 2 mac addresses and ignores traffic from any other mac address
- `(config-if)# switchport port-sec violation protect` sets it to protect mode
- `(config-if)# switchport port-sec max-addr sticky` i think this sets it to record the first n mac addrs that it sees, where n is the max you set up here ^^^


## Routing
Routers allow different vlans to communicate with eachother
For routers you need to set the gateway (router's ip) on the switches
So on the switch:
- `(config)# ip default-gateway <addr>`

### Loopback addresses
A loop back is a fake ip address that the router has, it allows you to test connection to the outside world even if you're not actually connected (just a fake ip is all you need to consider it as)
on a router:
- `(config)# int lo0`
- `(config-if)# desc <description>` not needed unless specified but could be useful
- `(conifg-if)# ip addr <addr> <subnet mask>`

### Router on a stick
ROT is essentially manual trunking, as you can't set a router's interface to trunk mode
This is done through sub interfaces
- `(config)# int g 0/0/1.99` where the .99 is the sub interface, use the vlan number as the sub-interface
- `(config-if)# desc <desc>` set this to something like "Sub interface for vlan 99"
- `(config-if)# encapsulation dot1q 99` this encapsulates the interface to vlan 99, the dot1q is the trunking protocol used (801.1q)
- `(config-if)# ip addr <addr> <subnet mask>`

### Other command
theres also a `trace rt <addr>` command which is probably important, look it up

## Arp
Will show the arp table (these are cmd/powershell/bash commands use on computer not switches/router)
- `$ arp -a`
Will delete the arp table
- `$ arp -d`

## Change system config
### Hostname
Go into admin mode, config mode, then change the hostname to 'Switch 3'
- `>ena`
- `#conf`
- `(config)#hostname Switch3`

### Motd:  
- `(config) $ This is the MOTD $` You don't have to use `$`, you can use any symbol on the outside

## Show config

### Running config

The running config is the current config, everything that you've configured during this session. This is stored in ram and will be overwritten on reboot.
- `>sh running-config`  
### Startup config

The start up config is the config that will be written to the running config on start-up.  
- `>sh startup-config`  
You can save the running config to the startup config through:  
- `# copy rujnning-config startup-config`  
- `# reload`
You will almost never have to do this.  

## Set password
You will never have to do this (most likely)
- `(config)# line console 0`  
- `(config-line)# password <password>` <password> could be any password like 'ccna'
- `(config-line)# login`

### Remove password

- `(config-line)# no password`
- `(config-line)# no login`
### admin password

- `(config)# enable secret <password>` <password> could be any password like 'ccna'
### disable admin password

- `(config)#no enable secret`

## Telnet
Telnet is an insecure, unencrypted way of getting a remote shell of another machine
This or openssh might be on the practical assessments

### Enabling (this might be incomplete)
- `(config)# line vty 0 15` (i'm unsure what the 0 15 does, check the lab stuff)
- `(config-line)# password <password>`
- `(config-line)# login`

### Remoting into another switch
- `>telnet <ip>` where <ip> is the ip of the switch you want to log into

### Disable telnet
- `(config)# line vty 0 15`
- `(config)# transport input none` 
- `(config)# no login` 

## SSH
ssh is a secure way of getting a remote shell of another machine

- `(config)# ip domain-name <name>` where name is a FQDN (I think you'll be told what name to make it)

### Keygen (Generate a secure crypto key for ssh to use)
- `# crypto key generate rsa general`
- `# keys modulus 1024` (I dont remeber if this is a seperate command to the last one oops) you might have to set a higher bit quanitity than 1024, 4096 is a reasonable amount

### Create a user
- `# username <username> privelege 15 secret <password>`

### Enable ssh
- `(config)# line vty 0 15`
- `(config-line)# transport input ssh`
- `(config-line)# login local`

### Set security features
- `(config)# ssh time-out 60`
- `(config)# ssh authentication-retries 4`

### Remoting into another switch
- `>ssh -L <username> <ip addr>`

### Disable ssh
- `(config)# line vty 0 15`
- `(config)# transport input none` 
- `(config)# no login` 

### Delete Key
- `(config)# crypto key zeroize`

## Spanning tree
I didn't put a lot in my lab journal for this so this might all be wrong
- `# sh spanning tree`

### Change the cost of an interface
- `(config-if)# spanning-tree cost <value>`

### Reset cost to default
- `(config-if)# default spanning-tree cost` idk if this is right

### Set root on vlan
- `(config)# spanning-tree vlan <num> root primary` can also be 'secondary'

### Enable port-fast
- `(config)# spanning-tree portfast`

## Ether channel
I didn't put a lot in my lab journal for this so this might all be wrong  
there are 2 types of ether channel, you'll be asked to do one of them.
difference is one is proprietry cisco, the other is an open standard implemented by most switches (idk which is which)

### PAgP
#### First switch
- `(config)# int range giga 1/0/5 - 6`
- `(config-if)# channel-group 1 mode desirable`
- `(config-if)# no shutdown`

#### Second switch
- `(config)# int range giga 1/0/5 - 6`
- `(config-if)# channel-group 1 mode auto`
- `(config-if)# no shutdown`

### LACP
#### First switch
- `(conifg)# int range giga 1/0/1 - 2`
- `(conifg)# switchport mode trunk`
- `(conifg)# switchport trunk native vlan 99`
- `(config)# channelgroup 2 mode active`
- `(config)# no shutdown`
#### Second switch
- `(conifg)# int range giga 1/0/1 - 2`
- `(conifg)# switchport mode trunk`
- `(conifg)# switchport trunk native vlan 99`
- `(config)# channelgroup 2 mode passive`
- `(config)# no shutdown`
