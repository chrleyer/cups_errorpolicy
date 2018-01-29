# cups_errorpolicy
Solution for CUPS disabling print queues / printer in case no paper, network failure or other issues

We have CUPS printing system on some Linux application servers mainly runnign odoo ERP. The systems are CentOS 6 based. Starting with CUPS 1.3, the CUPS print server defaults with an error policy that basically disables the printer when there's an error or interruption (USB cable disconnect, network error, no paper or paper jam, etc.). This default ErrorPolicy directive is in the printers config located at /etc/cups/printers.conf file.

```
<Printer Laserjet4000-Accounting>
Info Laserjet 4000T 3rd fl Accounting
DeviceURI socket://LJ4000ACC:9100
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

To change the default (global) error ploicy for new pinters:

Edit [/etc/cups/cupsd.conf]
```
# Set global Error Policy and retry interval
ErrorPolicy retry-job
JobRetryInterval 60
```

And remember to change the ErrorPolicy on all existing printers in /etc/cups/printers.conf

Restart cups service and you are all set.
