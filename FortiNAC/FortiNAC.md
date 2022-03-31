### L2 Polling
The three ways that L2 polling is triggered are:
• Manual polling: Manual polling is initiated when an administrative user right-clicks the device in the
topology tree and selects Poll for L2 (Hosts) Info, or clicks Network > L2 Polling.
• Scheduled: L2 polling is scheduled in the Network > L2 Polling view. You can change the default
scheduled intervals.
• Link Traps: Link traps received from an edge device trigger FortiNAC to perform an L2 poll to update its
awareness of devices that are connected on that edge device. The traps that trigger the poll are: Linkup,
Linkdown, WarmStart, and ColdStart. This trigger keeps FortiNAC up-to-date in real-time as devices
connect to and disconnect from edge device

### L3 Polling

***


### Change VLAN 
There are also some options that affect the management behavior for this device. If **VLAN Switching Enabled**
is not selected, FortiNAC will never change a VLAN on this device

***

### Persistent Agent (Setting server IP)
The behavior of the agent, and the FortiNAC server it communicates with, is configured in the **registry
on Windows systems**. Similar configurations are used on Mac systems and DNS SRV records can be
used. Installation scripts can be run on Linux systems for configuring these values
