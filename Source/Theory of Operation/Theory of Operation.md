# General Details

The NI VeriStand Telemetry Custom Device is an [Inline Hardware Interface Custom Device](http://zone.ni.com/reference/en-XX/help/372846M-01/veristandmerge/inline_hw_cds/), which allows it to read and write data to and from other parts of NI VeriStand in each iteration of the PCL. However, to avoid causing latency in the PCL that is associated with file I/O, the Telemetry Custom Device launches an asynchronous loop to log the data to a file.

## Custom Device Diagram

The following topics detail how this custom device uses an asynchronous loop and RT FIFOs, as well as where you can locate the code in the device that performs each task.

The following diagram illustrates how the Telemetry Custom Device uses an asynchronous loop to execute with respect to the PCL and how the Telemetry communicates to the asynchronous loop via RT FIFOs. To view the code used to launch the loop, open the Initialize Logger VI from the "Engine\Init\Initialize Logger.vi".

- RT Driver VI:

![](Resources\Telemetry_RT_Driver_VI_Diagram.PNG)

To view the RT Driver VI code open the VI from the "Engine\RT Driver VI.vi".

### Initialize

The Initialize case of the Telemetry RT Driver VI creates three RT FIFOs and launches an asynchronous loop. The Data RT FIFO communicates channel data from the Telemetry RT Driver VI to the asynchronous loop. The Status RT FIFO communicates state information from the asynchronous loop to the Telemetry RT Driver VI. The Async Closed RT FIFO is used to communicate when the asynchronous loop finished execution during the Close case.

To view the code for the asynchronous loop, open the Async Logger VI from the "Engine\Run\Async Logger.vi".

### Start

No code executes during this case.

### Read Data from HW

As previously mentioned, one of the RT FIFOs created in the Initialize case passes status information from the asynchronous loop to the Telemetry RT Driver VI. The reading of this RT FIFO takes place in the Read Data from HW case. In this case, the Telemetry checks the RT FIFO for an error code. If an error code, or timeout is reported, the error code is written in the Error Code channel.

### Write Data to HW

In the Write Data to HW case, for each PCL iteration, the VeriStand System Channels Data for all the VeriStand system channels is sent from the Inline Custom Device to the asynchronous loop, using the Data FIFO.

### Close

During the Close case of the RT Driver VI, we allow a delay for the asynchronous loop to read all the values from the Data FIFO, after which we Delete Data and Status FIFOs. This will trigger the shutdown phase for the asynchronous loop. We allow a maximum of 5 seconds for the shutdown, and after this time is reached, the Async Closed FIFO is deleted also.

## Asynchronous Loop

