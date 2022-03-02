Current BALTx Pools
======================
* https://pool.bmorecoin.com
* http://ondelay.ddns.net


BALTx Pool Instructions
======================
* Install bmorecoind https://medium.com/bmoretoken/just-released-a-cpu-mineable-coin-bmorecoin-1aa4e2c17303
* cd ~
* curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash
* sudo apt-get install -y nodejs
* sudo add-apt-repository ppa:chris-lea/redis-server
* sudo apt-get update
* sudo apt-get install redis-server
* sudo apt-get install libssl-dev
* sudo apt-get install libboost-all-dev
* sudo apt-get install libsodium-dev
* sudo apt-get install nmap apache2
* npm install dateformat@4.6.0
* npm i redis@3.1.2
* sudo adduser --disabled-password --disabled-login pool
* sudo sysctl vm.overcommit_memory=1
* sudo echo never > /sys/kernel/mm/transparent_hugepage/enabled
* sudo su - pool
* git clone https://github.com/BMoreToken/cryptonote-nodejs-pool.git pool
* cd pool
* npm update


## Starting - Stopping - Sync - Resync
* lsof -i -P -n | grep LISTEN

### redis server
* START: screen -S redis
* START: redis-server --port 6379
* STOP: ctrl-c

### bmorecoind
* EDIT ~/bmorecoin.conf ( make sure it matches walletd below )
<pre>
container-file = mycontainer
container-password = password
</pre>
* START: screen -S coin
* START: cd ~/bmorecoin/build/release/src; sudo ./bmorecoind
* DISCONNECT: ctrl-a ctrl-d
* RECONNECT screen -x coin
* STOP: ctrl-c

### walletd
* ONE TIME (take note of public address LOOKS LIKE  bxcWzipVmHPVmdrZW8rUEDffHaXgmNiRdU6k9hmVVgQy5DqFdwuoDz4dTFzvNXpayoifsDkxiehQpec1x8ByRNC52vypnzTNv ): cd ~/bmorecoin/build/release/src; ./walletd --container-file=mycontainer --container-password=password --generate-container
* START:  cd ~/bmorecoin/build/release/src; sudo ./walletd  --local  -c ~/bmorecoin.conf -l ~/walled.log --log-level 4  --p2p-bind-port 19127
* DISCONNECT: ctrl-a ctrl-d
* RECONNECT screen -x wallet
* STOP: ctrl-c

### pool
* su pool
* START: screen -S pool
* START: cd ~/pool
* ONE TIME: update config.json with address ( from above ), "poolHost" to match webserver
* ONE TIME: update website_example/config.js
* START: node init.js
* DISCONNECT: ctrl-a ctrl-d
* RECONNECT screen -x pool
* STOP: ctrl-c

### apache2 ( one time )
* nano /etc/apache2/sites-enabled/000-default.conf
* OLD:  DocumentRoot /var/www/html
* NEW:  DocumentRoot /home/pool/pool/website_example
* NEW: <pre>
&lt;Directory /&gt;
    Options Indexes FollowSymLinks Includes ExecCGI
    AllowOverride All
    Require all granted
    Allow from all
&lt;/Directory&gt;
</pre>
* sudo service apache2 restart




Credits
---------

* [fancoder](//github.com/fancoder) - Developper on cryptonote-universal-pool project from which current project is forked.
* [dvandal](//github.com/dvandal) - Developer of cryptonote-nodejs-pool software

License
-------
Released under the GNU General Public License v2

http://www.gnu.org/licenses/gpl-2.0.html
