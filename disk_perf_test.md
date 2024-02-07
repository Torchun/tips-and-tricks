### Prepare RAM disk with source file filled with random data (not zeroed!)
```
mkdir -p /tmp/ramdisk
mount -t tmpfs -o size=8192M tmpfs /tmp/ramdisk
df -h /tmp/ramdisk
echo "create urandom 8G file"
dd if=/dev/urandom of=/tmp/ramdisk/testfile bs=1M count=8k
ls -pla /tmp/ramdisk
```

### Download script
Do not forget to change target directory to be tested (on preferred disk).

```
# run nmon stats collection with slice of 1 seconnd, count of 3600 times.
/usr/bin/nmon -f -s 1 -c 3600 -l 64 -dYAVPMNWSN^ -m ./
NMON_PID=$(ps -ef | grep -i "/usr/bin/nmon" | grep -v grep | awk '{print $2}')
echo $NMON_PID

time for b in 512 1024 2048 4096 8192 16384 32768; do
  echo -e "\n===== block size ${b} =====\n"
  sleep 10
  
  SEQ=`seq 1 6` # 8*6 = 48 Gb
  echo -e "\n===== WRITE =====\n"
  for j in $SEQ; do
    ### CHANGE ">>" TO TARGET DISK MOUNT
    ### skip=1 means skip first block with bs block size - to prevent same bytes pattern
    dd if=/tmp/ramdisk/testfile bs=${b} skip=1 >> /tmp/storage.test ;
  done;
  # add skipped bytes 
  dd if=/tmp/ramdisk/testfile bs=65024 count=1 >> /tmp/storage.test ;
  
  echo -e "\n===== READ =====\n"
  dd if=/tmp/storage.test of=/dev/null bs=${b};

  # cleanup
  rm -f /tmp/storage.test;
  
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
 - [https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmonchart](https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmonchart)
