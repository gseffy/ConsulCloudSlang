This image is based on progrium/consul
it add java jdk and slang to the image.

I created this image to demonstrate the use of consul to monitor disk space of the docker machine and the use of slang to clear disk space if it needed.
It integrates consul with CloudSlang to create a consul instance that automatically clears unused Docker images, once a certain DiskSpace health check fails.

To run the demo on the docker machine:

1. Create local folder consul.d
`mkdir consul.d`

2. Add health check - json file consul.d/freespace.json
`{"check": {"name": "freespace", "script": "sh /consul.d/FS_monitor.sh", "interval": "200s","timeout": "100s"}}`

3. Add script to test the disk space consul.d/FS_monitor.sh
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
you can choose any usedthreshold between 0-100%

4. Add script to run slang consul.d/clearDiskSpace.sh 
`cslang run --f /cslang/content/io/cloudslang/docker/images/clear_docker_images_flow.sl --i docker_host=docker_host,docker_username=docker_username,docker_password=docker_password,private_key_file=/consul.d/xxxx.pem --cp /cslang/content/io/cloudslang/`
  * docker_host- the docker machine IP
  * docker_username- the docker machine user
  * docker_password- the docker machine password, if private key is used to login the machine the password is used as a key passphrase
  * private_key_file- absolute path to the key file on the docker container. you can add the key to consul.d folder and then give the path /consul.d/xxxx.pem
  
5. Run consul 
`docker run -p 8400:8400 -p 8500:8500 -p 8600:53/udp -v **PATHֹTOCONSUL.D/consul.d:/consul.d -h node1 gseffy/consulcloudslang -server -bootstrap -ui-dir /ui -config-dir /consul.d`
  * **PATHֹTOCONSUL.D the path to consul.d folder created on step 1 on the machine
  * now a new container is created and consul is running
