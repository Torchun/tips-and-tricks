### script
Do not forget to change target disk to be tested and to replace file with block device

```
# !!! CHANGE /dev/sdb TO PREFERRED DISK !!!
# run nmon stats collection with slice of 1 seconnd, count of 3600 times.
/usr/bin/nmon -f -s 1 -c 3600 -l 64 -dYAVPMNWSN^ -m ./
NMON_PID=$(ps -ef | grep -i "/usr/bin/nmon" | grep -v grep | awk '{print $2}')
echo $NMON_PID

# time for b in 512 1024 2048 4096 8192 16384 32768; do
time for b in 32768; do
  echo -e "\n===== block size ${b} =====\n"
  sleep 10
  COUNT=$(echo "1024*1024*1024*48 / ${b}" | bc)
  dd if=/dev/urandom of=/dev/sdb bs=${b} count=$COUNT ;
  
  echo -e "\n===== READ =====\n"
  dd if=/dev/sdb of=/dev/null bs=${b} count=$COUNT;

  sync;sync;
done

# stop nmon manually
kill -15 $NMON_PID

exit 0

```

### Analyze `*.nmon` file with Nmon Analyzer
 - [https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmon-Analyser](https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmon-Analyser)
 - [https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmonchart](https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmonchart)
