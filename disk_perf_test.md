### Prepare RAM disk with source file filled with random data (not zeroed!)
```
mkdir -p /tmp/ramdisk

mount -t tmpfs -o size=4096M tmpfs /tmp/ramdisk

dd if=/dev/urandom of=/tmp/ramdisk/testfile bs=1M count=4k
ls -pla /tmp/ramdisk
```


### Download script
Do not forget to change target directory to be tested (on preferred disk).

```
# run nmon stats collection with slice of 1 seconnd, count of 3600 times.
/usr/bin/nmon -f -s 1 -c 3600 -l 64 -dYAVPMNWSN^ -m ./
NMON_PID=$(ps -ef | grep -i "/usr/bin/nmon" | grep -v grep | awk '{print $2}')
echo $NMON_PID

time for b in 512 1024 4096 8192 16384 32768 65536 131072 262144 524288 1048576; do
  echo -e "\n===== block size ${b} =====\n"
  sleep 10
  SEQ=`seq 1 4`
  
  echo -e "\n===== WRITE =====\n"
  for j in $SEQ; do
    ### CHANGE "of=" TO TARGET DISK MOUNT
    dd if=/tmp/ramdisk/testfile of=/tmp/storage.${j}.test bs=${b} && sync && ls -pla /tmp/storage.${j}.test;
  done;
  
  echo -e "\n===== READ =====\n"
  for j in $SEQ; do
    dd if=/tmp/storage.${j}.test of=/dev/null bs=${b};
  done
  
  # cleanup
  for j in $SEQ; do
    rm -f /tmp/storage.${j}.test;
  done
  
  sync;sync;
done

# stop nmon manually
kill -15 $NMON_PID

exit 0
```

### Remove ramfs mount
```
umount /tmp/ramdisk
df -h | grep -v loop
```

### Analyze `*.nmon` file with Nmon Analyzer
 - [https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmon-Analyser](https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmon-Analyser)
 
 
 - 
