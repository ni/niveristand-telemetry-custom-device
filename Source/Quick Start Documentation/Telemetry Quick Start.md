# Overview

This NI VeriStand Custom Device provides a way of understanding the health of a VeriStand System by Logging System Channels data for each PCL iteration.

<br>

# Repo location

[GitHub](https://github.com/ni/niveristand-telemetry-custom-device)

---
<br>

# Execution

The Custom Device provides the data logging out of the box with minimal user configuration required.</br>
After adding the Custom Device you can already deploy the VeriStand System Definition and the Custom Device will start logging data to the TDMS file. When you undeploy, the file containing the logged data can be found at one of the following locations, depending on the Target type used:
- Windows: "<System Definition Folder Path>\Logs\telemetry.tdms"
- RT: "c:\ni-rt\NIVeriStand\Logs\telemetry.tdms" </br>
</br>
The list of System Channels that will be logged:</br>
		- Absolute Time </br>
      - Iteration </br>
      - Actual Loop Rate </br>
      - HP Loop Duration </br>
      - HP Count </br>
      - LP Count </br>
      - LP Data Count </br>
      - Failure Count </br>
      - HS TCP Overflow Count </br>
      - Model Count </br>
      - WPL Error Count </br>
      - WPL Overflow Count </br>
      - WPL TCP Overflow Count </br>
   <p>
   There is an option to keep only the latest file on the target, by having the "Overwrite File If Exists?" checked, or keep only the first created file, if you leave the control unchecked.
   </p>
   <p>
   <strong>Note:</strong> If the control will be unchecked no new file will be created during deployment, and no data logging will take place.
   </p>