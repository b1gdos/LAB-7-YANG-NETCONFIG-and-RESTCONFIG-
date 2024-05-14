# LAB-7-YANG-NETCONFIG-and-RESTCONFIG
  ## Part 1: Install the CSR1000v VM
  "This section of LAB 7 is perhaps the most challenging, which is why I recommend following the Cisco Academy guide to successfully navigate through it."
  ## Part 2: Explore YANG Models
   ```
   Objectives
   Part 1: Launch the DEVASC VM
   Part 2: Explore a YANG Model on GitHub
   Part 3: Explore a YANG Model Using pyang
   ```
    We gonna skipt to part 3, be sure to complete the task 2.
  ## Part 3: Explore a YANG Model Using pyang
  Verify that pyang is already installed with the ```pyang -v``` command.
  ![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/95bd58da-f5a5-4e66-93b1-cf78b51ce990)

    Check for any upgrade
Command: ```pip3 install pyang --upgrade```
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/0822230d-5df9-4093-a305-6a4613675a18)

    Navigate to the pyang directory
```
devasc@labvm:~/labs/devnet-src/pyang$ pyang -h | more
Usage: pyang [options] [<filename>...]
Validates the YANG module in <filename> (or stdin), and all its dependencies.
Options:
 -h, --help Show this help message and exit
 -v, --version Show version number and exit
<output omitted>
 -f FORMAT, --format=FORMAT
 Convert to FORMAT. Supported formats are: yang, yin,
dsdl, jstree, jsonxsl, capability, identifiers, jtox,
uml, name, omni, tree, depend, sample-xml-skeleton
<output omitted>
devasc@labvm:~/labs/devnet-src/pyang$
```
Transform the ietf-interfaces.yang model into a tree format with the following command. Notice that the
leaf enabled is much easier to find and read in this format.
```devasc@labvm:~/labs/devnet-src/pyang$ pyang -f tree ietf-interfaces.yang```
![image](https://github.com/b1gdos/LAB-7-YANG-NETCONFIG-and-RESTCONFIG-/assets/70448476/f8357f43-2cc8-436a-a53d-e12ecb98506d)

## Conclusions

Using Pyang for Cisco devices offers several advantages. First, it allows for comprehensive validation of YANG modules against the YANG specifications, ensuring compliance with standards. Second, Pyang facilitates the generation of human-readable documentation from YANG modules, aiding in understanding and implementing device configurations. Third, it supports the validation of YANG modules against industry-specific guidelines, promoting interoperability and adherence to best practices. Overall, Pyang enhances the development and management of Cisco devices by providing robust validation and documentation capabilities for YANG models.
