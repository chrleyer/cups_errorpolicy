# cups_errorpolicy
Solution for CUPS disabling print queues / printers in case of no paper, network failure or other issues.

We use the CUPS printing system on Linux application servers running odoo ERP to print some forms on dotmatrix printers. The systems are CentOS 6 based. Starting with CUPS 1.3, the CUPS print server defaults with an error policy that basically disables the printer when there's an error or interruption (USB cable disconnect, network error, no paper or paper jam, etc.). This default ErrorPolicy directive is in the printers config located at ``/etc/cups/printers.conf`` file.

```
<Printer Epson-LQ300-WHDD>
Info Epson LQ-300 3rd fl DD Warehouse
DeviceURI lpd://EPSDM-DDWH1/br80060150
State Idle
Accepting Yes
Shared Yes
JobSheets none none
QuotaPeriod 0
PageLimit 0
KLimit 0
OpPolicy default
ErrorPolicy stop-printer
</Printer>
```

First check your CUPS version with: 
```
cups-config --version
```
This ErrorPolicy handling is only available from CUPS version 1.3 and above.

To change the default (global) error policy for new pinters:

Edit [``/etc/cups/cupsd.conf``]
```
# Set global Error Policy and retry interval
ErrorPolicy retry-job
JobRetryInterval 60
```

And remember to change the ErrorPolicy on all existing printers in ``/etc/cups/printers.conf``

Restart cups service and you are all set.
```
service cups restart
```


``Description from the CUPS source code:``

The ErrorPolicy directive defines the default policy that is used when a
   backend is unable to send a print job to the printer.

The following values are supported:

 * abort-job - Abort the job and proceed with the next job in the queue
 * retry-job - Retry the job after waiting for N seconds; the cupsd.conf
   JobRetryInterval directive controls the value of N
 * retry-this-job - Retry the current job immediately and indefinitely.
 * stop-printer - Stop the printer and keep the job for future printing;
   this is the default value

Examples

 * ErrorPolicy abort-job
 * ErrorPolicy retry-job
 * ErrorPolicy stop-printer

