Rnode on raspberry pi 3b
-----
Deploying [Rchain node](https://github.com/kayvank/rchain) on [Raspberry pi 3b+](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/)

this deployment is based on locally built of my [rchain fork](https://github.com/kayvank/rchain/tree/raspberry-pi) raspberry-pi branch

## Deployment steps:
* OS installation
* Prerequisite libraries and tools
* set up classpath
* rchain build

### OS installation
node requires a 64 bit architecture. I found [openSUSE](https://www.opensuse.org/) to be an excellent choice for 64bit Arm architecture. 

Installation binaries are found at: [rasperry pi openSUSE](https://en.opensuse.org/HCL:Raspberry_Pi3) 

For more detail on OS installation refer to:
* [HCL Raspberry Pi3](https://en.opensuse.org/HCL:Raspberry_Pi3)
* [installation video](https://www.youtube.com/watch?v=UA9ByJwWhzs) 

#### OS Specs and Version:

```
pi@lucid-pi:~> cat /etc/os-release
NAME="openSUSE Leap"
VERSION="42.3"
ID=opensuse
ID_LIKE="suse"
VERSION_ID="42.3"
PRETTY_NAME="openSUSE Leap 42.3"
ANSI_COLOR="0;32"
CPE_NAME="cpe:/o:opensuse:leap:42.3"
BUG_REPORT_URL="https://bugs.opensuse.org"
HOME_URL="https://www.opensuse.org/"
```
### Prerequisite
Install the following libraries and packages on your pi

#### [YaSt installs](https://en.opensuse.org/Portal:YaST):
* jdk8-devel
* automake cmake
* apr-util 
* ninja
* gcc c++
* lmdb-devel 
* openSSL
* libtool
* tls 

#### Manual Installs
* Download and install [libsodium](https://download.libsodium.org/doc/installation/ ) as outlined in the wiki
* clone, build and install netty-tcnative all its associated modules:

```
git clone git@github.com:netty/netty-tcnative.git
cd netty-tcnative
./mvnw compile
sudo ./mvnw install
```
##### Note
netty-tcnative installation is crucial and will take several hours. The installation is very OS and architecture specifics.  Make sure you follow the guide lines outlined in the [wiki](http://netty.io/wiki/forked-tomcat-native.html).

### CLASSPATH
add these to your ~/.profile
```
export M2_HOME=$HOME/.m2
export JAVA_OPTS="-Xms256m -Xmx512m"
export CLASSPATH="$CLASSPATH:$HOME/.local/share/java/java-cup-11b.jar"
export CLASSPATH="$CLASSPATH:$HOME/.local/share/java/java-cup-11b-runtime.jar"
export CLASSPATH=$CLASSPATH:$M2_HOME/repository/io/netty/netty-tcnative-boringssl-static/2.0.13.Final-SNAPSHOT/netty-tcnative-boringssl-static-2.0.13.Final-SNAPSHOT-linux-aarch_64.jar
export CLASSPATH=$CLASSPATH:$M2_HOME/repository/io/netty/netty-tcnative-boringssl-static/2.0.13.Final-SNAPSHOT/netty-tcnative-boringssl-static-2.0.13.Final-SNAPSHOT.jar
export SBT_OPTS="-Xms256m -Xmx512m"
```

### Deploy Rchain
* deploy the pi-built [image](./bin-image) as outlined below
* deploy your locally-built image

#### deploy pi-built image
Note that this is a special build of the rchain rnode for the purpose of the raspberry pi.  You may simply run the image as outlined below.  To run node on pi, follow [rchain rnode exexution instruction](https://github.com/rchain/rchain/tree/dev/node#32-bootstrapping-a-private-network)

```
git clone  git@github.com:kayvank/rchain-node-bin.git
cd rchain-node
run ./bin/rnode -s -p 4000
```

#### deploy locally-built image
* This is based on my [fork of the rchain](https://github.com/kayvank/rchain/tree/raspberry-pi) project.  

```
git clone git@github.com:kayvank/rchain.git
## make sure you use the raspberry-pi branch
git checkout -b raspberry-pi && git pull origin raspberry-pi  
```

follow [Developers guide](https://github.com/kayvank/rchain/tree/raspberry-pi#deverloper-guide)

##### NOTE

This is very hacky.

Manually modify the [rnode script](./bin/rnode) script and add the system CLASSPATH, [set](#classpath). See line [341 EOL](https://github.com/kayvank/rchain-node-bin/blob/master/bin-image/bin/rnode#L341) for detail.
