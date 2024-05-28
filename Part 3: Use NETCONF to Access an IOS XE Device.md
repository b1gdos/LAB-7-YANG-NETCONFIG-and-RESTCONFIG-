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

#### Conver the XML output in a more readable file
 ``` 
 <?xml version="1.0" encoding="UTF-8"?>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="103"><data><interfaces xmlns="urn:ietf:params:xml:ns:yang:ietf-interfaces"><interface><name>GigabitEthernet1</name><description>VBox</description><type xmlns:ianaift="urn:ietf:params:xml:ns:yang:iana-if-type">ianaift:ethernetCsmacd</type><enabled>true</enabled><ipv4 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"></ipv4><ipv6 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"></ipv6></interface></interfaces></data></rpc-reply>
 ```
 *I use a tool to convert the output in a more readable xml
 https://www.samltool.com/prettyprint.php*

```
 <?xml version="1.0" encoding="UTF-8"?>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="103">
  <data>
    <interfaces xmlns="urn:ietf:params:xml:ns:yang:ietf-interfaces">
      <interface>
        <name>GigabitEthernet1</name>
        <description>VBox</description>
        <type xmlns:ianaift="urn:ietf:params:xml:ns:yang:iana-if-type">ianaift:ethernetCsmacd</type>
        <enabled>true</enabled>
        <ipv4 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"/>
        <ipv6 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"/>
      </interface>
    </interfaces>
  </data>
</rpc-reply>
 ```

### Step 5: Close the NETCONF session
#### a. To close the NETCONF session, the client needs to send the following RPC message:
```
<rpc message-id="9999999" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
 <close-session />
</rpc>
```
#### b. After a few seconds, you will be returned to the terminal prompt. Return to the CSR1kv prompt and show the open netconf sessions. You will see that the session has been closed.
##### CSR1kv# show netconf-yang sessions
There are no active sessions
##### CSR1kv#
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/f4a0fa13-44af-4138-b7f4-63714b6c90b2)

## Part 3: Use ncclient to Connect to NETCONF
#### Step 1: Verify that ncclient is installed and ready for use.
In a DEVASC-VM terminal, enter the command pip3 list --format=columns to see all Python modules
currently installed. Pipe the output to more. Your output may differ from the following. But you should see
ncclient listed, as shown. If not, use the pip3 install ncclient command to install it.

![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/4e0d0d05-decc-4057-96ac-be5ae8e22b39)

*Make one script to test the connection via ncclient*
``` 
from ncclient import manager

m = manager.connect(
    host="192.168.56.101",
    port=830,
    username="cisco",
    password="cisco123!",
    hostkey_verify=False
)
```
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/eff3d798-5fa0-4233-9cd7-12703ff1aebb)
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/7c5969b5-b75a-495e-a052-cc859373c675)

#### Step 3: Add a print function to the script so that the NETCONF capabilities for the CSR1kv are  listed.

#### The m object returned by the manager.connect() function represents the NETCONF remote session. As you saw previously, in every NETCONF session, the server first sends its capabilities, which is a list, in XML format, of supported YANG models. With the ncclient module, the received list of capabilities is stored in the 
m.server_capabilities list.
``` 
from ncclient import manager

m = manager.connect(
    host="192.168.56.101",
    port=830,
    username="cisco",
    password="cisco123!",
    hostkey_verify=False
)
print("#Supported Capabilities (YANG models):")
for capability in m.server_capabilities:
    print(capability)
```

## Part 4: Use ncclient to Retrieve the Configuration
```
#Script
from ncclient import manager

m = manager.connect(
    host="192.168.56.101",
    port=830,
    username="cisco",
    password="cisco123!",
    hostkey_verify=False
)
'''print("#Supported Capabilities (YANG models):")
for capability in m.server_capabilities:
    print(capability)'''
netconf_reply = m.get_config(source="running")
print(netconf_reply)

```

*To make my life easier I did export the output to a file*
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/44f5b718-a010-417a-9781-f44824f15ca6)

*The XML print is show like this*
```
<?xml version="1.0" encoding="UTF-8"?>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:2c9c19f4-8ef6-4c1e-b045-8484baa6444d">
  <data>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
      <version>16.9</version>
      <boot-start-marker/>
      <boot-end-marker/>
      <banner>
        <motd>
          <banner>^C</banner>
        </motd>
      </banner>
      <service>
        <timestamps>
          <debug>
            <datetime>
              <msec/>
            </datetime>
          </debug>
          <log>
            <datetime>
              <msec/>
            </datetime>
          </log>
        </timestamps>
      </service>
      <platform>
        <console xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-platform">
          <output>virtual</output>
        </console>
      </platform>
      <hostname>CSR1kv</hostname>
      <username>
        <name>cisco</name>
        <privilege>15</privilege>
        <password>
          <encryption>0</encryption>
          <password>cisco123</password>
        </password>
      </username>
      <ip>
        <domain>
          <name>example.netacad.com</name>
        </domain>
        <forward-protocol>
          <protocol>nd</protocol>
        </forward-protocol>
        <http xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-http">
          <authentication>
            <local/>
          </authentication>
          <server>false</server>
          <secure-server>true</secure-server>
        </http>
      </ip>
      <interface>
        <GigabitEthernet>
          <name>1</name>
          <description>VBox</description>
          <ip>
            <address>
              <dhcp/>
            </address>
          </ip>
          <mop>
            <enabled>false</enabled>
            <sysid>false</sysid>
          </mop>
          <negotiation xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-ethernet">
            <auto>true</auto>
          </negotiation>
        </GigabitEthernet>
      </interface>
      <control-plane/>
      <login>
        <on-success>
          <log/>
        </on-success>
      </login>
      <multilink>
        <bundle-name xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-ppp">authenticated</bundle-name>
      </multilink>
      <redundancy/>
      <spanning-tree>
        <extend xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-spanning-tree">
          <system-id/>
        </extend>
      </spanning-tree>
      <subscriber>
        <templating/>
      </subscriber>
      <crypto>
        <pki xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-crypto">
          <trustpoint>
            <id>TP-self-signed-3722712040</id>
            <enrollment>
              <selfsigned/>
            </enrollment>
            <revocation-check>none</revocation-check>
            <rsakeypair>
              <key-label>TP-self-signed-3722712040</key-label>
            </rsakeypair>
            <subject-name>cn=IOS-Self-Signed-Certificate-3722712040</subject-name>
          </trustpoint>
          <certificate>
            <chain>
              <name>TP-self-signed-3722712040</name>
              <certificate>
                <serial>01</serial>
                <certtype>self-signed</certtype>
              </certificate>
            </chain>
          </certificate>
        </pki>
      </crypto>
      <license>
        <udi>
          <pid>CSR1000V</pid>
          <sn>9ZIOUI0CCVD</sn>
        </udi>
      </license>
      <line>
        <console>
          <first>0</first>
          <logging>
            <synchronous/>
          </logging>
          <stopbits>1</stopbits>
        </console>
        <vty>
          <first>0</first>
          <last>4</last>
          <login>
            <local/>
          </login>
          <transport>
            <input>
              <input>ssh</input>
            </input>
          </transport>
        </vty>
        <vty>
          <first>5</first>
          <last>15</last>
          <login>
            <local/>
          </login>
          <transport>
            <input>
              <input>ssh</input>
            </input>
          </transport>
        </vty>
      </line>
      <diagnostic xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-diagnostics">
        <bootup>
          <level>minimal</level>
        </bootup>
      </diagnostic>
    </native>
    <licensing xmlns="http://cisco.com/ns/yang/cisco-smart-license">
      <config>
        <enable>false</enable>
        <privacy>
          <hostname>false</hostname>
          <version>false</version>
        </privacy>
        <utility>
          <utility-enable>false</utility-enable>
        </utility>
      </config>
    </licensing>
    <interfaces xmlns="http://openconfig.net/yang/interfaces">
      <interface>
        <name>GigabitEthernet1</name>
        <config>
          <name>GigabitEthernet1</name>
          <type xmlns:ianaift="urn:ietf:params:xml:ns:yang:iana-if-type">ianaift:ethernetCsmacd</type>
          <description>VBox</description>
          <enabled>true</enabled>
        </config>
        <subinterfaces>
          <subinterface>
            <index>0</index>
            <config>
              <index>0</index>
              <description>VBox</description>
              <enabled>true</enabled>
            </config>
            <ipv6 xmlns="http://openconfig.net/yang/interfaces/ip">
              <config>
                <enabled>false</enabled>
              </config>
            </ipv6>
          </subinterface>
        </subinterfaces>
        <ethernet xmlns="http://openconfig.net/yang/interfaces/ethernet">
          <config>
            <mac-address>08:00:27:41:77:60</mac-address>
            <auto-negotiate>true</auto-negotiate>
          </config>
        </ethernet>
      </interface>
    </interfaces>
    <network-instances xmlns="http://openconfig.net/yang/network-instance">
      <network-instance>
        <name>default</name>
        <config>
          <name>default</name>
          <type xmlns:oc-ni-types="http://openconfig.net/yang/network-instance-types">oc-ni-types:DEFAULT_INSTANCE</type>
          <description>default-vrf [read-only]</description>
        </config>
        <tables>
          <table>
            <protocol xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:DIRECTLY_CONNECTED</protocol>
            <address-family xmlns:oc-types="http://openconfig.net/yang/openconfig-types">oc-types:IPV4</address-family>
            <config>
              <protocol xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:DIRECTLY_CONNECTED</protocol>
              <address-family xmlns:oc-types="http://openconfig.net/yang/openconfig-types">oc-types:IPV4</address-family>
            </config>
          </table>
          <table>
            <protocol xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:DIRECTLY_CONNECTED</protocol>
            <address-family xmlns:oc-types="http://openconfig.net/yang/openconfig-types">oc-types:IPV6</address-family>
            <config>
              <protocol xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:DIRECTLY_CONNECTED</protocol>
              <address-family xmlns:oc-types="http://openconfig.net/yang/openconfig-types">oc-types:IPV6</address-family>
            </config>
          </table>
          <table>
            <protocol xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:STATIC</protocol>
            <address-family xmlns:oc-types="http://openconfig.net/yang/openconfig-types">oc-types:IPV4</address-family>
            <config>
              <protocol xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:STATIC</protocol>
              <address-family xmlns:oc-types="http://openconfig.net/yang/openconfig-types">oc-types:IPV4</address-family>
            </config>
          </table>
          <table>
            <protocol xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:STATIC</protocol>
            <address-family xmlns:oc-types="http://openconfig.net/yang/openconfig-types">oc-types:IPV6</address-family>
            <config>
              <protocol xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:STATIC</protocol>
              <address-family xmlns:oc-types="http://openconfig.net/yang/openconfig-types">oc-types:IPV6</address-family>
            </config>
          </table>
        </tables>
        <protocols>
          <protocol>
            <identifier xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:STATIC</identifier>
            <name>DEFAULT</name>
            <config>
              <identifier xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:STATIC</identifier>
              <name>DEFAULT</name>
            </config>
          </protocol>
          <protocol>
            <identifier xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:DIRECTLY_CONNECTED</identifier>
            <name>DEFAULT</name>
            <config>
              <identifier xmlns:oc-pol-types="http://openconfig.net/yang/policy-types">oc-pol-types:DIRECTLY_CONNECTED</identifier>
              <name>DEFAULT</name>
            </config>
          </protocol>
        </protocols>
      </network-instance>
    </network-instances>
    <interfaces xmlns="urn:ietf:params:xml:ns:yang:ietf-interfaces">
      <interface>
        <name>GigabitEthernet1</name>
        <description>VBox</description>
        <type xmlns:ianaift="urn:ietf:params:xml:ns:yang:iana-if-type">ianaift:ethernetCsmacd</type>
        <enabled>true</enabled>
        <ipv4 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"/>
        <ipv6 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"/>
      </interface>
    </interfaces>
    <nacm xmlns="urn:ietf:params:xml:ns:yang:ietf-netconf-acm">
      <enable-nacm>true</enable-nacm>
      <read-default>deny</read-default>
      <write-default>deny</write-default>
      <exec-default>deny</exec-default>
      <enable-external-groups>true</enable-external-groups>
      <rule-list>
        <name>admin</name>
        <group>PRIV15</group>
        <rule>
          <name>permit-all</name>
          <module-name>*</module-name>
          <access-operations>*</access-operations>
          <action>permit</action>
        </rule>
      </rule-list>
    </nacm>
    <routing xmlns="urn:ietf:params:xml:ns:yang:ietf-routing">
      <routing-instance>
        <name>default</name>
        <description>default-vrf [read-only]</description>
        <routing-protocols>
          <routing-protocol>
            <type>static</type>
            <name>1</name>
          </routing-protocol>
        </routing-protocols>
      </routing-instance>
    </routing>
  </data>
</rpc-reply>
```
#### Step 2: Use Python to prettify the XML
```
#Script
from ncclient import manager
import xml.dom.minidom

m = manager.connect(
    host="192.168.56.101",
    port=830,
    username="cisco",
    password="cisco123!",
    hostkey_verify=False
)
'''print("#Supported Capabilities (YANG models):")
for capability in m.server_capabilities:
    print(capability)'''
netconf_reply = m.get_config(source="running")
'''print(netconf_reply)''' #Replace for a xml.dom.minidom version
print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())

```
#### Step 3: Use a filter with get_config() to only retrieve a specific YANG model.
A network administrator may only want to retrieve a portion of the running configuration on a device. NETCONF supports returning only data that is defined in a filter parameter of the get_conf() function. 

a. Create a variable called netconf_filter that only retrieves data defined by the Cisco IOS XE Native YANG
model.

```
from ncclient import manager
import xml.dom.minidom
netconf_filter = """
<filter>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native" />
</filter>
"""
m = manager.connect(
    host="192.168.56.101",
    port=830,
    username="cisco",
    password="cisco123!",
    hostkey_verify=False
)
'''print("#Supported Capabilities (YANG models):")
for capability in m.server_capabilities:
    print(capability)'''
# netconf_reply = m.get_config(source="running")
'''print(netconf_reply)''' #Replace for a xml.dom.minidom version
'''print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())'''

netconf_reply = m.get_config(source="running", filter=netconf_filter)
print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())
```
## Part 5: Use ncclient to Configure a Device

#### b. Use the edit_config() function of the m NETCONF session object to send the configuration and store the results in the netconf_reply variable so that they can be printed. The parameters for the edit_config() 
##### function are as follows:
*target - the targeted NETCONF datastore to be updated*
*config - the configuration modification that is to be sent*

*netconf_reply = m.edit_config(target="running", config=netconf_hostname)*

#### c. The edit_config() function returns an XML RPC reply message with <ok/> indicating that the change was
successfully applied. Repeat the previous print statement to display the results.
*print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())*

``` 
#Script to modify hostname
from ncclient import manager
import xml.dom.minidom
'''netconf_filter = """
<filter>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native" />
</filter>
"""
'''
netconf_hostname = """
<config>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
        <hostname>NEWHOSTNAME</hostname>
    </native>
</config>
"""
m = manager.connect(
    host="192.168.56.101",
    port=830,
    username="cisco",
    password="cisco123!",
    hostkey_verify=False
)
'''print("#Supported Capabilities (YANG models):")
for capability in m.server_capabilities:
    print(capability)'''
# netconf_reply = m.get_config(source="running")
'''print(netconf_reply)''' #Replace for a xml.dom.minidom version
'''print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())'''

netconf_reply = m.edit_config(target="running", config=netconf_hostname)
print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())
```
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/2a1306a9-0967-4cf6-bec3-c88e55084f2a)

### Step 2: Use ncclient to create a new loopback interface on R1.

#### a. Create a new <config> variable to hold the configuration for a new loopback interface. Add the following to your ncclient_netconf.py script. 
```
from ncclient import manager
import xml.dom.minidom
'''netconf_filter = """
<filter>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native" />
</filter>
"""
'''
'''netconf_hostname = """
<config>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
        <hostname>CSR1kv</hostname>
    </native>
</config>
"""
'''
netconf_loopback = """
<config>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
        <interface>
            <Loopback>
                <name>1</name>
                <description>My first NETCONF loopback</description>
                <ip>
                    <address>
                        <primary>
                            <address>10.1.1.1</address>
                            <mask>255.255.255.0</mask>
                        </primary>
                    </address>
                </ip>
            </Loopback>
        </interface>
    </native>
</config>
"""
m = manager.connect(
    host="192.168.56.101",
    port=830,
    username="cisco",
    password="cisco123!",
    hostkey_verify=False
)
'''print("#Supported Capabilities (YANG models):")
for capability in m.server_capabilities:
    print(capability)'''
# netconf_reply = m.get_config(source="running")
'''print(netconf_reply)''' #Replace for a xml.dom.minidom version
'''print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())'''

'''netconf_reply = m.get_config(source="running", filter=netconf_filter)'''
'''netconf_reply = m.edit_config(target="running", config=netconf_hostname)'''
netconf_reply = m.edit_config(target="running", config=netconf_loopback)
print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())
```
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/e8621705-26e4-438c-9ce3-a9813017f71b)
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/705105f7-5e86-4639-8a96-eb4d0cd41e07)

#### Step 3: Attempt to create a new loopback interface with the same IPv4 address.
*I was trying to create a loopback interface, but although there were no error messages, the loopback wasn't created*
