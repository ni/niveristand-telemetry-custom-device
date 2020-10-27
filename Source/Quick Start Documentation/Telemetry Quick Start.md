# Overview

This NI VeriStand Custom Device provides a way of understanding the health of a VeriStand System by Logging System Channels data for each PCL iteration.

<br>

# Repo location

[GitHub](https://github.com/ni/niveristand-telemetry-custom-device)

---
<br>

# Execution

The Custom Device provides the data logging out of the box with minimal user configuration required.</br>
Upon adding you can deploy your VeriStand system and based on the target type used (Windows or RT) after you undeploy you can find the logged data in one of the following locations:
- Windows: "<System Definition Folder Path>\Logs\telemetry.tdms"
- RT: "c:\ni-rt\NIVeriStand\Logs\telemetry.tdms" </br>
</br>
The list of System Channels that are logged:
- 


