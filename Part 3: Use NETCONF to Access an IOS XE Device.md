# Lab - Use NETCONF to Access an IOS XE Device
## Objectives

```
    Part 1: Build the Network and Verify Connectivity
    Part 2: Use a NETCONF Session to Gather Information
    Part 3: Use ncclient to Connect to NETCONF
    Part 4: Use ncclient to Retrieve the Configuration
    Part 5: Use ncclient to Configure a Device
    Part 6: Challenge: Modify the Program Used in This Lab
```
### Jump to Part 2: Use a NETCONF Session to Gather Information
```NETCONF may already be running if another student enabled it, or if a later IOS version enables it by default. From your SSH session with the CSR1kv, use the show platform software yang-management process command to see if the NETCONF SSH daemon (ncsshd) is running.```

        show platform software yang-management process
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/b7232fe3-d763-4668-9fe2-ec17cf063492)

## Step 2: Access the NETCONF process through an SSH terminal.
In this step, you will re-establish an SSH session with the CSR1kv. But this time, you will specify the
NETCONF port 830 and send netconf as a subsystem command.
Note: For more information on these options, explore the manual pages for SSH (man ssh).
   
#### a. Enter the following command in a terminal window. You can use the up arrow to recall the latest SSH command and just add the -p and -s options as shown. Then, enter ```cisco123!``` as the password.

    devasc@labvm:~$ ssh cisco@192.168.56.101 -p 830 -s netconf
    cisco@192.168.56.101's password:
   

#### b. The CSR1kv will respond with a hello message that includes over 400 lines of output listing all of its NETCONF capabilities. The end of NETCONF messages are identified with ```]]>]]>```.

```
<?xml version="1.0" encoding="UTF-8"?>
<hello xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
<capabilities>
<capability>urn:ietf:params:netconf:base:1.0</capability>
<capability>urn:ietf:params:netconf:base:1.1</capability>
<capability>urn:ietf:params:netconf:capability:writable-running:1.0</capability>
<capability>urn:ietf:params:netconf:capability:xpath:1.0</capability>
<capability>urn:ietf:params:netconf:capability:validate:1.0</capability>
<capability>urn:ietf:params:netconf:capability:validate:1.1</capability>
(output omitted)
 </capability>
</capabilities>
<session-id>20</session-id></hello>]]>]]>
```
## Step 3: Start a NETCONF session by sending a hello message from the client.
To start a NETCONF session, the client needs to send its own hello message. The hello message should
include the NETCONF base capabilities version the client wants to use.

#### a. Copy and paste the following XML code into the SSH session. Notice that the end of the client hello message is identified with a ]]>]]>.
    
```
<hello xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
 <capabilities>
   <capability>urn:ietf:params:netconf:base:1.0</capability>
 </capabilities>
</hello>
]]>]]>
```
I stop using vs for this task, cause i was getting errors when i was trying to give the commands, in the bash terminal all the commands works perfectly.

![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/9bb1e894-13f8-4d0d-9d05-fb90f4cf236b)
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/093a9449-c88c-436d-9927-4bcc9834c761)


#### b. Switch to the CSR1kv VM and use the show netconf-yang sessions command to verify that a NETCONF session has been started. If the CSR1kv VM screen is dark, press Enter to wake it up.
```
CSR1kv> en
CSRk1v# show netconf-yang sessions
```
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/28d741b5-aeee-40bc-868a-af0095057d66)

## Step 4: Send RPC messages to an IOS XE device.
**During an SSH session, a NETCONF client can use Remote Procedure Call (RPC) messages to send
NETCONF operations to the IOS XE device. The table lists some of the more common NETCONF operations.**
| Operation | Description|
------------|-------------
| ```<get>``` |Retrieve running configuration and device state information|
| ```<get-config>``` | Retrieve all or part of a specified configuration data store|
| ```<edit-config>``` | Loads all or part of a configuration to the specified configuration data store|
| ```<copy-config>``` | Replace an entire configuration data store with another|
| ```<delete-config>``` | Delete a configuration data store|
| ```<commit>``` | Copy candidate data store to running data store| 
| ```<lock> / <unlock>``` | Lock or unlock the entire configuration data store system|
| ```<close-session>``` | Graceful termination of NETCONF session |
| ```<kill-session>``` | Forced termination of NETCONF session|

#### a. Copy and paste the following RPC get message XML code into the terminal SSH session to retrieve information about the interfaces on R1.
```
<rpc message-id="103" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
 <get>
  <filter>
    <interfaces xmlns="urn:ietf:params:xml:ns:yang:ietf-interfaces"/>
   </filter>
  </get>
</rpc>
]]>]]>
```
    
