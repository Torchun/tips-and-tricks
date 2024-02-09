### before
```
apt install screen nmon sysstat bc

mkdir -p /ramdisk
mount -t tmpfs -o size=12288M tmpfs /ramdisk
df -h /ramdisk
lsblk -b -io KNAME,TYPE,SIZE,MODEL | awk 'BEGIN{OFS="\t"} {if (FNR>1) print $1,$2,$3/1073741824"G",$4; else print $0}'
```
```
# generate files 4Gb size
free -h
CACHE_GB=12
SIZE_GB=4
FILE_COUNT=$(echo "${CACHE_GB} / ${SIZE_GB}" | bc)
for j in `seq 1 ${FILE_COUNT}`; do dd if=/dev/urandom of=/ramdisk/${j} bs=1M count=${SIZE_GB}k; done
free -h
```

### script
Do not forget to change target disk to be tested and to replace file with block device

```
# !!! CHANGE /dev/vdd TO PREFERRED DISK !!!
IO_TARGET=/dev/vdd
CACHE_GB=12
SIZE_GB=4
FILE_COUNT=$(echo "${CACHE_GB} / ${SIZE_GB}" | bc)
GB=50 # amount of gigabytes to test

# flush caches
echo 3 > /proc/sys/vm/drop_caches
free -h

# re-fill caches
for j in `seq 1 ${FILE_COUNT}`; do dd if=/ramdisk/${j} of=/dev/null bs=10M; done
free -h

# run nmon stats collection with slice of 1 seconnd, count of 86400 times == 24h.
/usr/bin/nmon -f -s 1 -c 86400 -l 64 -dYAVPMNWSN^ -m ./
NMON_PID=$(ps -ef | grep -i "/usr/bin/nmon" | grep -v grep | awk '{print $2}')
echo $NMON_PID

DATE=$(date +'%y%d%m_%H%M')
iostat -x -d ${IO_TARGET} -o JSON -t 1 > ./iostat_${DATE}.json & IOSTAT_PID=$!
echo $IOSTAT_PID



time for b in 4096 8192 16384 32768; do
  echo -e "\n===== block size ${b} =====\n"
  sleep 10

  echo -e "\n===== WRITE =====\n"
  for j in `seq 1 ${GB}`; do
    RANDOMFILE=$((1 + $RANDOM % ${CACHE_GB}))
    dd if=/ramdisk/${RANDOMFILE} of=${IO_TARGET} bs=${b}
  done
  
  echo -e "\n===== READ =====\n"
  COUNT=$(echo "1024*1024*1024*${GB} / ${b}" | bc)
  dd if=${IO_TARGET} of=/dev/null bs=${b} count=$COUNT;

  sync;sync;
done

# stop nmon manually
kill -15 $NMON_PID
kill -SIGINT $IOSTAT_PID

exit 0
```

### Analyze `*.nmon` file with Nmon Analyzer
 - [https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmon-Analyser](https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmon-Analyser)
 - [https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmonchart](https://nmon.sourceforge.io/pmwiki.php?n=Site.Nmonchart)
