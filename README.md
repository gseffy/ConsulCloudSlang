This image is based on progrium/consul
it add java jdk and slang to the image.

I created this image to demonstrate the use of consul to monitor disk space of the docker machine and the use of slang to clear disk space if it needed.
It integrates consul with CloudSlang to create a consul instance that automatically clears unused Docker images, once a certain DiskSpace health check fails.

To run the demo on the docker machine:

1. <p>Create local folder consul.d
`mkdir consul.d`</p>

2. <p>Add health check - json file consul.d/freespace.json
`{"check": {"name": "freespace", "script": "sh /consul.d/FS_monitor.sh", "interval": "200s","timeout": "100s"}}`</p>

3. <p>Add script to test the disk space consul.d/FS_monitor.sh 

``` bash
let usedthreshold=5
for free in `df | grep -v -e "/boot" -e "/dev/shm" -e "Used" | awk '{print $5}' | cut -d"%" -f1`
do
        if [ $free -ge $usedthreshold ]
        then
                        echo $free
                        exit 1
        fi
done
echo 0
exit 0 
```  
you can choose any usedthreshold between 0-100% </p>
4. jkgjghj <br/>
5. ghjghjgj
