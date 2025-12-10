## Topology & Scenario
1x Multilayer Switch (Core): Configured as VTP Server.

2x Access Switches: Configured as VTP Clients.

VTP Domain: cisco

VTP Password: cisco

Links: All inter-switch connections are configured as Trunks (802.1Q).

## Configuration Workflow
1. VTP Server Configuration
The Multilayer Switch was set up as the VTP Server. This switch is responsible for creating, deleting, and modifying VLANs for the entire domain.

Mode: Server

Trunk Check: Verified interfaces Gig0/1 and Gig0/2 are in trunking mode using sh int trunk.

Initial Status: Configuration Revision started at 0 with 5 existing VLANs.

2. VTP Client Configuration
The access switches were joined to the VTP domain.

Mode: Client

Verification: Attempting to create a VLAN on the client switch resulted in the expected error:

VTP VLAN configuration not allowed when device is in CLIENT mode. (This confirms the client is correctly restricting local VLAN database modification.)

3. VLAN Creation and Propagation
New VLANs were added to the Server to test synchronization.

Action: Created VLAN 2 and VLAN 3 on the Server.

Observation: The Configuration Revision number on the Server incremented with every change:

Initial: 0

After changes: Incremented to 1, then 2. (This indicates the VTP advertisements were successfully sent to the domain.)

4. Port Assignment on Clients
Once the VLANs propagated to the Client switches, access ports were assigned to specific VLANs.

Interface fa0/1: Assigned to VLAN 2

Interface fa0/2: Assigned to VLAN 3

## Key Commands Used
Here are the primary commands utilized during this lab:

## Bash

# --- VTP Setup (Server & Client) ---
Switch(config)# vtp mode [server | client]
Switch(config)# vtp domain cisco
Switch(config)# vtp password cisco

# --- Creating VLANs (Server Only) ---
Switch(config)# vlan 2
Switch(config)# exit
Switch(config)# vlan 3
Switch(config)# exit

# --- Verification ---
Switch# show vtp status      ! Check mode, domain, and revision number
Switch# show vtp password    ! Verify password
Switch# show vlan            ! See active VLANs
Switch# show interfaces trunk ! Verify trunk links

# --- Assigning Ports (Client) ---
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 2


## Conclusion
Synchronization: The Client switches successfully synchronized their VLAN database with the Server.

Security: The Client mode correctly prevented unauthorized VLAN creation on edge switches.

Automation: VLANs 2 and 3 were available on the Client switches without manual local creation, streamlining the network administration.
