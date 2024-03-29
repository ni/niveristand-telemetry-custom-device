# Overview

The Telemetry Custom Device enables data logging functionality to help you understand the health of a VeriStand system. Out of the box, this custom device allows system channel logging for each PCL iteration and the ability to monitor target resources.

# Repo location

[GitHub: ni/niveristand-telemetry-custom-device](https://github.com/ni/niveristand-telemetry-custom-device)

# Execution

After adding the custom device, you can deploy the system definition in VeriStand to begin logging data to a TDMS file. When you undeploy the system definition, you can access this file.

Depending on the target type, the log file can be found in one of the following locations:
- **Windows:** "&lt;System Definition Folder Path&gt;\Logs\telemetry.tdms"
- **RT:** "c:\ni-rt\NIVeriStand\Logs\telemetry.tdms"

## Logged Data
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
- CPU &lt;XXX&gt; Usage (%), where &lt;XXX&gt; represents the number of the CPU on the target system.

By default, this custom device will overwrite the TDMS file created on the target during each deployment. To keep the first file instead, disable **Overwrite File If Exists?**.

**Note:** If **Overwrite File If Exists?** is not enabled and a log file is already present at the target location, no new file will be created during deployment. Instead, the **Error Code** Channel will report *Error 6: Generic File I/O Error*.

## Disk Size Requirements

The resulting TDMS log file size depends on the system configuration and runtime. You can estimate the file size using the following formula:

- File Size [bytes] = &lt;TNC&gt; * &lt;Target Rate&gt; [Hz] * &lt;Running Time&gt; [sec] * 8 [bytes]

where:
- &lt;TNC&gt; represents the total number of logged channels. This number is the sum of 13 System Channels, 1 for Physical Memory usage, and the total number of target CPU cores running in the VeriStand system.
- &lt;Target Rate&gt; represents the VeriStand system's run rate.
- &lt;Running Time&gt; represents the deployed system's runtime.
- 8 [bytes] represents the disk size for one double-precision, floating-point (DBL) numeric value. All logged channels from the file are DBL.

The following example is of a size calculation for a standard quad core system running at 1 [kHz] for a minute:

- File Size [bytes] = 18 * 1000 * 60 * 8 = 8,640,000 [bytes] = 8.24 [MB]

## Error Code Channel
The **Error Code** channel reports the first internal error encountered during the execution of the custom device. The channel reports general LabVIEW error codes and custom error codes.

### Custom Error Codes:
| Code  | Description |
|-------|-------------|
| -8000 | Data Write Timeout. VeriStand is pushing data faster than the custom device can handle. System channel data will be missing for some iterations of the VeriStand PCL. |
