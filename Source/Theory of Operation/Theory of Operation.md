# General Details

The NI VeriStand Telemetry Custom Device is an [Inline Hardware Interface Custom Device](http://zone.ni.com/reference/en-XX/help/372846M-01/veristandmerge/inline_hw_cds/), which allows it to read and write data to and from other parts of NI VeriStand in each iteration of the PCL. However, to avoid causing latency in the PCL that is associated with file I/O, the Telemetry Custom Device launches an asynchronous loop to log the data to a file.

The following topics detail how this custom device uses an asynchronous loop and RT FIFOs, as well as where you can locate the code in the device that performs each task.

# RT Driver

The following diagram illustrates how the Telemetry Custom Device uses an asynchronous loop to execute with respect to the PCL and how the Telemetry communicates to the asynchronous loop via RT FIFOs.

![RT Driver Diagram](./Resources/RT_Driver_VI_Diagram.png)

To view the RT Driver VI code open the VI from the "Engine\RT Driver VI.vi".

## Initialize

In the "Initialize" case of the Telemetry RT Driver VI three RT FIFOs are created and the asynchronous loop is launched. The Data RT FIFO communicates channel data from the Telemetry RT Driver VI to the asynchronous loop. The Status RT FIFO communicates state information from the asynchronous loop to the Telemetry RT Driver VI. The Async Closed RT FIFO is used to communicate when the asynchronous loop finished execution in the Close case.

To view the code for the asynchronous loop, open the Async Logger VI from the "Engine\Run\Async Logger.vi".

## Start

No Telemetry specific code executes in this case.

## Read Data from HW

As previously mentioned, the Status RT FIFO created in the Initialize case passes status information from the asynchronous loop to the Telemetry RT Driver VI. The Status RT FIFO is read in the "Read Data from HW" case. Here, the Telemetry checks the RT FIFO for an error code. If an error code, or timeout is reported, the error code is written in the Error Code channel.

## Write Data to HW

In the "Write Data to HW" case, for each PCL iteration, all the VeriStand system channels data is sent from the RT Driver to the asynchronous loop, using the Data FIFO.

## Close

During the Close case of the RT Driver VI, a delay is introduced to let the asynchronous loop to read and log all the values from the Data FIFO, after which we delete both Data and Status FIFOs. This will trigger the shutdown phase for the asynchronous loop. When the Async Close acknowledge is received, or the maximum defined time is reached, the Async Closed FIFO is deleted also.

# Asynchronous Loop

The asynchronous loop, Async Logger VI, is a VI that runs asynchronous to the PCL Loop. It contains a while loop for Data Processing and Logging, and a timed loop used for reading the CPU and RAM usage data.

The Timed Loop will continually read and update the CPU-RAM FIFO with CPU and RAM usage data with a period of 400 milliseconds for RT Targets, and 2 seconds for Windows. The size of this FIFO is 1, meaning that only the latest value inside this RT FIFO is kept.

To view the code used to launch the Async Logger, open the Initialize Logger VI from the "Engine\Init\Initialize Logger.vi". Inside this VI you can view the default data used to initialize the Async Logger. The Buffers.Size is a dynamic value calculated from the Engine Rate value set inside the System Definition. For a Windows Target, Buffers.Size will be twice the Engine Rate, in order to write the Buffers.LOG Data array each 2 seconds, as to be in sync with the CPU and RAM timed loop reading. For RT Targets, Buffers.Size is half the Engine Rate, in order to write the Buffers.LOG Data close to the 400 ms timed loop update of the CPU-RAM usage update rate.

The following diagram shows the states of the Data Processing and Logging Loop as this is the main execution loop for this VI.

![Async Logger Diagram](./Resources/Async_Logger_VI_Diagram.png)

## Initialize

In the "Initialize" case of the Async Logger the file reference for the TDMS log file will be generated and the properties for all the expected channels and channel groups will be set, as well, as initializing the buffers needed for the logged data.

## Read System Channels FIFO

In the "Read System Channels FIFO" case of the Async Logger all the System Channels data for one PCL Loop that is sent from the "Write Data to HW" case is read. The data is buffered in the "Buffers.All System Channels Data" array. If the buffer array size is reached, the "Filter System Channels" case will execute next. If the RT Data FIFO was deleted, signaling that the VeriStand system is undeploying, the shutdown phase is initiated by selecting the execution of the "Filter System Channels Data", and enabling "is Shutdown" boolean.

## Filter System Channels

In the "Filter System Channels" case of the Async Logger, the data for the expected VeriStand channels that are relevant for benchmarking is filtered and copied to the Buffers.LOG Data array. After this case finishes execution, the "Read CPU-RAM FIFO" case will execute next.

## Read CPU-RAM FIFO

In the "Read CPU-RAM FIFO" case of the Async Logger, the Buffers.LOG Data array is populated with the CPU and RAM usage values read from the CPU-RAM RT FIFO.

## Log Data

In the "Log Data" case of the Async Logger, the Buffers.LOG Data array is written to the TDMS Log File, and the Buffers.Current Index is reset to 0. If "is Shutdown" boolean is true, then the Shutdown case will be executed next, otherwise, the "Read System Channels" is next.

## Shutdown

In the Shutdown case, we close the TDMS file reference and stop the Data Processing and Logging Loop. After the Data Processing and Logging Loop stopped, the Deletion of the CPU-RAM FIFO will generate an RT FIFO Write error in the CPU and RAM reading Loop that will stop this loop also. After both loops stopped, a boolean Acknowledged is sent to the RT Driver VI.