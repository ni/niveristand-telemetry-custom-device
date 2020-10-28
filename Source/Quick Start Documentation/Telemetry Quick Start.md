# Overview

This NI VeriStand Custom Device provides a way of understanding the health of a VeriStand System by Logging System Channels for each PCL iteration.

# Repo location

[GitHub: ni/niveristand-telemetry-custom-device](https://github.com/ni/niveristand-telemetry-custom-device)

# Execution

The Telemetry Custom Device provides data logging out of the box with minimal user configuration required.

After adding the Custom Device you can deploy the VeriStand System Definition and the Custom Device will start logging data to a TDMS file. When you undeploy, the file containing the logged data will be found at one of the below locations, depending on the type of Target used:
- <strong>Windows: </strong>"&lt;System Definition Folder Path&gt;\Logs\telemetry.tdms"
- <strong>RT: </strong>"c:\ni-rt\NIVeriStand\Logs\telemetry.tdms"

## Logged System Channels:
- Absolute Time
- Iteration
- Actual Loop Rate
- HP Loop Duration
- HP Count
- LP Count
- LP Data Count
- Failure Count
- HS TCP Overflow Count
- Model Count
- WPL Error Count
- WPL Overflow Count
- WPL TCP Overflow Count

   There is an option to keep only the latest file on the target, by having the **Overwrite File If Exists?** checked, or keep only the first created file, if you leave the control unchecked.
   
   **Note:** If **Overwrite File If Exists?** is unchecked and a log file already exists at the specific location, no new file will be created during deployment, and no data logging will take place.