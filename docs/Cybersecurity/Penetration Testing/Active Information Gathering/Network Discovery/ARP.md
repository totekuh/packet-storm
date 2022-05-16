## Sweeping the network with msf

```bash
msf use auxiliary/scanner/discovery/arp_sweep
set RHOSTS 192.168.1.200-254 
set SHOST 192.168.1.101 
set SMAC d6:46:a7:38:15:65 
set THREADS 55 
run
```