# Overview

The Telemetry Custom Device enables data logging functionality to help you understand the health of a VeriStand system. It provides the ability to log system channels for each PCL iteration out of the box.

# Repo location

[GitHub: ni/niveristand-telemetry-custom-device](https://github.com/ni/niveristand-telemetry-custom-device)

# Execution

After adding the custom device, you can deploy the system definition in VeriStand to begin logging data to a TDMS file. When you undeploy the system definition, you can access this file.

Depending on the target type, the log file can be found in the following locations:
- **Windows:** "&lt;System Definition Folder Path&gt;\Logs\telemetry.tdms"
- **RT:** "c:\ni-rt\NIVeriStand\Logs\telemetry.tdms"

## Logged System Channels:
You can log the following system channels:
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

By default, this custom device only retains the first TDMS file created on the target. To keep the latest file instead, enable **Overwrite File If Exists?**.

 **Note:** If **Overwrite File If Exists?** is not enabled and a log file is present at the target location, no new file will be created during deployment.
