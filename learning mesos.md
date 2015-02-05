# Learning Mesos

## Why Mesos?
To allow multiple frameworks to share a single cluster.

[Learning Apache Mesos]: http://radar.oreilly.com/2014/01/learning-apache-mesos.html
[Open source datacenter computing with Apache Mesos]: http://opensource.com/business/14/9/open-source-datacenter-computing-apache-mesos

## Installation
- use a pre-configured vagrant box

```
~/Projects/mesos/ubtu1404-dsktp-docker-mesos vagrant init ubtu1404-dsktp-docker
```
- activate gui by un-marking gui option in Vagrantfile

### install/update docker 
- to new version [docker on ubuntu]

[docker on ubuntu]: https://docs.docker.com/installation/ubuntulinux/#ubuntu-trusty-1404-lts-64-bit
[install meson on ubuntu]: https://mesosphere.com/docs/tutorials/install_ubuntu_debian/
[repository setup]: http://mesosphere.com/downloads/details/index.html#apache-mesos

- click `start tutorial` on [install meson on ubuntu] 
- [repository setup] to get a key and update

```
### Setup
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv E56151BF
DISTRO=$(lsb_release -is | tr '[:upper:]' '[:lower:]')
CODENAME=$(lsb_release -cs)

### Add the repository
echo "deb http://repos.mesosphere.io/${DISTRO} ${CODENAME} main" | \
  sudo tee /etc/apt/sources.list.d/mesosphere.list
sudo apt-get -y update
```

### Install mesos and marathon

```
vagrant@vagrant-vm:/tmp$ sudo apt-get -y install mesos marathon
Reading package lists... Done
...[snip]...
```
### Install Chronos

```
sudo apt-get -y install chronos 
```

### reboot
`sudo reboot` is not enough, `vagrant halt` then `vagrant up`

### vm IPAddress

```
vagrant@vagrant-vm:~$ ifconfig
docker0   Link encap:Ethernet  HWaddr 56:84:7a:fe:97:99  
          inet addr:172.17.42.1  Bcast:0.0.0.0  Mask:255.255.0.0
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

eth0      Link encap:Ethernet  HWaddr 08:00:27:fd:2b:02  
          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fefd:2b02/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:4370 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2519 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:3044449 (3.0 MB)  TX bytes:257312 (257.3 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
```

See [single node installation]

[single node installation]: http://mesosphere.com/docs/getting-started/developer/single-node-install/

To test Mesos installation with `mesos-execute`,

```
vagrant@vagrant-vm:~$ sudo service mesos-master start
mesos-master start/running, process 7272
vagrant@vagrant-vm:~$ sudo service mesos-slave start
mesos-slave start/running, process 7310
vagrant@vagrant-vm:~$ sudo service marathon start
marathon start/running, process 7337
```
Define MASTER

```
vagrant@vagrant-vm:~$ MASTER=$(mesos-resolve `cat /etc/mesos/zk`)
2015-01-11 21:03:34,698:7402(0x7fa5efbf8700):ZOO_INFO@log_env@712: Client environment:zookeeper.version=zookeeper C client 3.4.5
2015-01-11 21:03:34,698:7402(0x7fa5efbf8700):ZOO_INFO@log_env@716: Client environment:host.name=vagrant-vm
2015-01-11 21:03:34,698:7402(0x7fa5efbf8700):ZOO_INFO@log_env@723: Client environment:os.name=Linux
2015-01-11 21:03:34,698:7402(0x7fa5efbf8700):ZOO_INFO@log_env@724: Client environment:os.arch=3.13.0-24-generic
2015-01-11 21:03:34,699:7402(0x7fa5efbf8700):ZOO_INFO@log_env@725: Client environment:os.version=#46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014
2015-01-11 21:03:34,699:7402(0x7fa5efbf8700):ZOO_INFO@log_env@733: Client environment:user.name=vagrant
2015-01-11 21:03:34,699:7402(0x7fa5efbf8700):ZOO_INFO@log_env@741: Client environment:user.home=/home/vagrant
2015-01-11 21:03:34,699:7402(0x7fa5efbf8700):ZOO_INFO@log_env@753: Client environment:user.dir=/home/vagrant
2015-01-11 21:03:34,699:7402(0x7fa5efbf8700):ZOO_INFO@zookeeper_init@786: Initiating client connection, host=localhost:2181 sessionTimeout=10000 watcher=0x7fa5f55bc6a0 sessionId=0 sessionPasswd=<null> context=0x7fa5e8001790 flags=0
2015-01-11 21:03:34,704:7402(0x7fa5ed3b8700):ZOO_INFO@check_events@1703: initiated connection to server [127.0.0.1:2181]
2015-01-11 21:03:34,706:7402(0x7fa5ed3b8700):ZOO_INFO@check_events@1750: session establishment complete on server [127.0.0.1:2181], sessionId=0x14adc8425f70008, negotiated timeout=10000
WARNING: Logging before InitGoogleLogging() is written to STDERR
I0111 21:03:34.707176  7411 group.cpp:313] Group process (group(1)@127.0.1.1:33000) connected to ZooKeeper
I0111 21:03:34.707316  7411 group.cpp:790] Syncing group operations: queue size (joins, cancels, datas) = (0, 0, 0)
I0111 21:03:34.707368  7411 group.cpp:385] Trying to create path '/mesos' in ZooKeeper
I0111 21:03:34.708658  7411 detector.cpp:138] Detected a new leader: (id='0')
I0111 21:03:34.708852  7411 group.cpp:659] Trying to get '/mesos/info_0000000000' in ZooKeeper
I0111 21:03:34.709700  7411 detector.cpp:433] A new leading master (UPID=master@127.0.1.1:5050) is detected
```
per packaged marathon is verion 0.76, in 

```
vagrant@vagrant-vm:~$ which marathon
/usr/local/bin/marathon
```
test,

```
vagrant@vagrant-vm:~$ mesos-execute --master=$MASTER --name="cluster-test" --command="sleep 5"
I0111 21:10:53.723057  7494 sched.cpp:137] Version: 0.21.1
I0111 21:10:53.725951  7501 sched.cpp:234] New master detected at master@127.0.1.1:5050
I0111 21:10:53.726207  7501 sched.cpp:242] No credentials provided. Attempting to register without authentication
I0111 21:10:53.727676  7501 sched.cpp:408] Framework registered with 20150111-210154-16842879-5050-7272-0001
Framework registered with 20150111-210154-16842879-5050-7272-0001
task cluster-test submitted to slave 20150111-210154-16842879-5050-7272-S0
Received status update TASK_RUNNING for task cluster-test
Received status update TASK_FINISHED for task cluster-test
I0111 21:10:59.377588  7497 sched.cpp:1286] Asked to stop the driver
I0111 21:10:59.377773  7497 sched.cpp:752] Stopping framework '20150111-210154-16842879-5050-7272-0001'
vagrant@vagrant-vm:~$ 
```

We can see a task is completed in screenshot ![installation test task]
The test result ![mesos test result]

[installation test task]: https://www.evernote.com/shard/s302/sh/29ad105e-d9c2-474d-92c2-f4e1b811399c/14941f3f4c52f85b12f89c80036f5a8d/deep/0/ubtu1404-dsktp-docker-mesos_default_1421032591373_52828--Running-.png

[mesos test result]: https://www.evernote.com/shard/s302/sh/c652fc10-2f54-440d-843f-3797b1a5cae8/cb79c7b4f5fad1db5118914bd59e252e/deep/0/ubtu1404-dsktp-docker-mesos-2_default_1421204939809_53072--Running-.png


### Config of Docker Container

see instruction for [Launching a Docker Container on Mesosphere]

[Launching a Docker Container on Mesosphere]: http://mesosphere.com/docs/tutorials/launch-docker-container-on-mesosphere/

Test Docker installation 
`sudo docker run -i -t libmesos/ubuntu /bin/bash`
This will pull image to local Docker repo; this may solve the time out problem.

```
vagrant@vagrant-vm:~$ echo "docker,mesos" | sudo tee /etc/mesos-slave/containerizers
docker,mesos
vagrant@vagrant-vm:~$ ls /etc/mesos-slave/
containerizers
vagrant@vagrant-vm:~$ cat /etc/mesos-slave/containerizers 
docker,mesos
vagrant@vagrant-vm:~$ echo "5mins" | sudo tee /etc/mesos-slave/executor_registration_timeout
5mins
vagrant@vagrant-vm:~$ sudo service mesos-slave restart
mesos-slave stop/waiting
mesos-slave start/running, process 3123
vagrant@vagrant-vm:~$ 
```
Submit a job thru Marathon,

```
vagrant@vagrant-vm:/vagrant$ curl -i -X POST -H 'Content-Type: application/json' --data-binary @app.json http://localhost:8080/v2/apps
HTTP/1.1 201 Created
Location: http://localhost:8080/v2/apps/rails-demo
Content-Type: application/json
Transfer-Encoding: chunked
Server: Jetty(8.y.z-SNAPSHOT)

{"id":"/rails-demo","cmd":"rails server -p $PORT","args":null,"user":null,"env":{},"instances":1,"cpus":0.01,"mem":256.0,"disk":0.0,"executor":"","constraints":[],"uris":[],"storeUrls":[],"ports":[3000],"requirePorts":false,"backoffSeconds":1,"backoffFactor":1.15,"container":{"type":"DOCKER","volumes":[],"docker":{"image":"superguenter/demo-app","network":null,"portMappings":null,"privileged":false,"parameters":{}}},"healthChecks":[],"dependencies":[],"upgradeStrategy":{"minimumHealthCapacity":1.0},"version":"2015-01-14T07:14:57.246Z"}vagrant@vagrant-vm:/vagrant$ 
```

pulling rail-demo ![pulling rail-demo]

[pulling rail-demo]: https://www.evernote.com/shard/s302/sh/29c240f1-9490-4f17-ac75-93bd520e83b0/e82d457594cfc09a43daa106d4fcdddb/deep/0/ubtu1404-dsktp-docker-mesos-2_default_1421204939809_53072--Running-.png

Example from digitalocean:

This service will work just fine. However, if we truly want to emulate the service we created in the web UI, we have to add some additional fields. These were defaulted in the web UI and we can replicate them here:
```
{ "id": "hello2", "cmd": "echo hello; sleep 10", "mem": 16, "cpus": 0.1, "instances": 1, "disk": 0.0, "ports": [0] }
```
Next, we can submit it using the Marathon API. The target is one of our master's Marathon service at port 8080 and the endpoint is /v2/apps. The data payload is our JSON file, which we can read into curl by using the -d flag with the @ flag to indicate a file.
The command to submit will look like this:

`curl -i -H 'Content-Type: application/json' -d@hello2.json 192.168.2.1:8080/v2/apps`

```
vagrant@vagrant-vm:/vagrant$ curl -i -H 'Content-Type: application/json' -d@hello2.json localhost:8080/v2/apps
HTTP/1.1 201 Created
Location: http://localhost:8080/v2/apps/hello2
Content-Type: application/json
Transfer-Encoding: chunked

Server: Jetty(8.y.z-SNAPSHOT)
{"id":"/hello2","cmd":"echo hello; sleep 10","args":null,"user":null,"env":{},"instances":1,"cpus":0.1,"mem":16.0,"disk":0.0,"executor":"","constraints":[],"uris":[],"storeUrls":[],"ports":[0],"requirePorts":false,"backoffSeconds":1,"backoffFactor":1.15,"container":null,"healthChecks":[],"dependencies":[],"upgradeStrategy":{"minimumHealthCapacity":1.0},"version":"2015-01-14T19:11:11.456Z"}vagrant@vagrant-vm:/vagrant$
vagrant@vagrant-vm:/vagrant$
```

the result ![hello2 screenshot]

[hello2 screenshot]: https://www.evernote.com/shard/s302/sh/465b14d4-0d07-4781-890c-053cf3a357d9/3a87d1d63356b9a7f611bb804c12e6f8/deep/0/ubtu1404-dsktp-docker-mesos-2_default_1421204939809_53072--Running-.png

## Concepts
 
### Scalable Infrastructure Ingridents
- Packaging: Immutable artificts (JARs, Dockers, ...)
- Depolyment orchestration
- Health checks
- Automated failure handling, 
- Service discovery, dynamic composition in real-time.

Service design, test, creation and delivery time shortened and steps merged.
Good decision processes need to be built in, feedback loop is necessary. automated failure discovery is must.

Service can not identified by hostname alone, host is moving around 
License is not fixed to a machine, assigned dynamically

### Mesos is...
- a top-level Apache project
- a cluster resource broker
- scalar, fault-tolerant, battle-test
- an sdk for distributed apps

- like a kernal for DCOS (Data Center OS)

### Say hi to Marathon 
- A self-serve interface to you cluster
- Distributed "init" for long-running services
- A private fault-tolerant PaaS

- Marathon is a framework for mesos - a scheduler

### Marathron Concept
- An app describes a task (a blueprint)
- A task is an instance (with PID) of an app
- Marathon creates tasks for app

We can see mesos actions ![mesos api sequences].

[mesos api sequences]: https://www.evernote.com/shard/s302/sh/b4ef2988-786d-45c4-954f-00452cd01c39/a99fcaffa5239388597607cd27e9014a/deep/0/Scalable-Infrastructure-with-Apache-Mesos,-Marathon,-and-Docker---Tobi-Knaup---Part-1---Velocity-Conference-New-York-2014--Complete-Video-Compilation.png

1. Marathon receives resource request in Json format
2. Marathon communicates with Mesos and receives a resource offer
3. Marathon will issue the command to Mesos to launch the Task
4. Mesos will send command to Executor to launch the task, this is related to specific box or server
5. Marathon will receive notification of the status of resource (i.e. failed,... ) from Mesos, which is sourced from Executor

### What Marathon do
- Start, stop, scale, update apps
- View running tasks
- Kill an individual task
- Has nice UI and API

### How
- has an Event bus, event can be subscribed, for example to find out what happen to task
- app can be versioned
- highly available, no SPoF

## Tutorial from Velocity
Scripts for the demo is at [velecity gist]
[velecity gist]: https://gist.github.com/guenter/1ac7b1d57ac6cadbdb5b

### Launch Docker Container
- port is a type of resource

- login the mesos-vm and post the app.json file. This did not work, we are not in the right directory.

```
~/Projects/mesos/ubtu1404-dsktp-docker-mesos vagrant ssh
Welcome to Ubuntu 14.04 LTS (GNU/Linux 3.13.0-24-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

461 packages can be updated.
154 updates are security updates.

Last login: Sat Jan  3 07:54:23 2015 from 10.0.2.2
vagrant@vagrant-vm:~$ curl -i -X POST -H 'Content-Type: application/json' --data-binary @app.json http://10.0.2.15:8080/v2/apps
Warning: Couldn't read data from file "app.json", this makes an empty POST.
HTTP/1.1 500 Internal Server Error
Content-Type: application/json
Transfer-Encoding: chunked
Server: Jetty(8.y.z-SNAPSHOT)

{"message":"No content to map due to end-of-input\n at [Source: [B@4d5ec0c6; line: 1, column: 1]"}vagrant@vagrant-vm:~$ l
Desktop/  Documents/  Downloads/  examples.desktop  Music/  Pictures/  Projects/  Public/  Templates/  Videos/
```
Move to the share folder - vagrant and resubmit.

```
vagrant@vagrant-vm:~$ cd /vagrant/
vagrant@vagrant-vm:/vagrant$ l
app.json  Vagrantfile
vagrant@vagrant-vm:/vagrant$ curl -i -X POST -H 'Content-Type: application/json' --data-binary @app.json http://10.0.2.15:8080/v2/apps
HTTP/1.1 201 Created
Location: http://10.0.2.15:8080/v2/apps/rails-demo
Content-Type: application/json
Transfer-Encoding: chunked
Server: Jetty(8.y.z-SNAPSHOT)

{"id":"/rails-demo","cmd":"rails server -p $PORT","args":null,"user":null,"env":{},"instances":1,"cpus":0.01,"mem":256.0,"disk":0.0,"executor":"","constraints":[],"uris":[],"storeUrls":[],"ports":[3000],"requirePorts":false,"backoffSeconds":1,"backoffFactor":1.15,"container":{"type":"DOCKER","volumes":[],"docker":{"image":"superguenter/demo-app","network":null,"portMappings":null,"privileged":false,"parameters":{}}},"healthChecks":[],"dependencies":[],"upgradeStrategy":{"minimumHealthCapacity":1.0},"version":"2015-01-11T19:29:40.564Z"}vagrant@vagrant-vm:/vagrant$
```

There is an error `unable to remount sys readonly: unable to mount sys as readonly max retries reached`
In terminal, `echo 'DOCKER_OPTS="$DOCKER_OPTS -e lxc"' | sudo tee -a /etc/default/docker > /dev/null` then re-start docker `sudo service docker restart`

The potential problem is old Docker version in pre-installed in Ubuntu, re-created a vm with newer Docker, worked.

you can see the example has been deployed in mesos. ![velocity demo example deployed]

[velocity demo example deployed]: https://www.evernote.com/shard/s302/sh/9207aa9f-1d6b-459e-8da1-16fcdacf6c7c/9529d53ff09cab0151fd42a2ab3d3eda/deep/0/ubtu1404-dsktp-docker-mesos_default_1420299612683_23553--Running-.png




