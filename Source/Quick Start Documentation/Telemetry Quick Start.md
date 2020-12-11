# Overview

The Telemetry Custom Device enables data logging functionality to help you understand the health of a VeriStand system. It provides out of the box the ability to log system channels for each PCL iteration, and to monitor the target resources.

# Repo location

[GitHub: ni/niveristand-telemetry-custom-device](https://github.com/ni/niveristand-telemetry-custom-device)

# Execution

After adding the custom device, you can deploy the system definition in VeriStand to begin logging data to a TDMS file. When you undeploy the system definition, you can access this file.

Depending on the target type, the log file can be found in the following locations:
- **Windows:** "&lt;System Definition Folder Path&gt;\Logs\telemetry.tdms"
- **RT:** "c:\ni-rt\NIVeriStand\Logs\telemetry.tdms"

## Logged data:
### System Channels:
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

### Target Resources:
- Physical Memory Usage (%)
- CPU x Usage (%) - where x represents each CPU present on the target.

By default, this custom device will overwrite the TDMS file created on the target during each deployment. To keep the first file instead, disable **Overwrite File If Exists?**.

**Note:** If **Overwrite File If Exists?** is not enabled and a log file is present at the target location, no new file will be created during deployment. The **Error Code** Channel will report *Error 6: Generic File I/O Error*.
 
## Error Code Channel
The **Error Code** Channel reports the first internal error encountered during the execution of the Custom Device. It reports general LabVIEW error codes, and custom error codes.
 
### Custom Error Codes:
| Code  | Description |
|-------|-------------|
| -8000 | Data Write Timeout. VeriStand system pushes data faster than the Custom Device can handle. This will result in missing system channels data for some specific iterations of the VeriStand PCL. |