Current BALTx Pools
======================
* https://pool.bmorecoin.com ( US-EAST )
* http://ondelay.ddns.net ( US-WEST )
* http://157.230.249.94/ ( Singapore )

BALTx Pool Instructions ( Use UBUNTU 18.04 LTR )
======================
* $5 Digital Ocean "Out of Memory": fallocate -l 1G /swapfile
* $5 Digital Ocean "Out of Memory": chmod 600 /swapfile
* $5 Digital Ocean "Out of Memory": mkswap /swapfile
* $5 Digital Ocean "Out of Memory": swapon /swapfile
* $5 Digital Ocean "Out of Memory": echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
* apt-get install -y libboost-all-dev g++ gcc cmake wget libbz2-dev
* cd ~
* git clone https://github.com/BMoreToken/bmorecoin
* cd bmorecoin
* make
* cd ~
* sudo apt-get install -y nodejs
* sudo add-apt-repository ppa:chris-lea/redis-server
* sudo apt-get update
* sudo apt-get install -y redis-server
* sudo apt-get install -y libssl-dev
* sudo apt-get install -y libsodium-dev
* sudo apt-get install -y nmap apache2 npm
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
* Check ports: lsof -i -P -n | grep LISTEN

### bmorecoind
* nano ~/bmorecoin.conf ( make sure it matches walletd below )
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
* ONE TIME: update config.json with pool wallet address ( from above ), poolHost to match webserver
* ONE TIME: update website_example/config.js set api & poolHost
* START: node init.js
* DISCONNECT: ctrl-a ctrl-d
* RECONNECT screen -x pool
* STOP: ctrl-c

### apache2 ( one time )
* nano /etc/apache2/sites-enabled/000-default.conf
* OLD:  DocumentRoot /var/www/html
* NEW:  DocumentRoot /home/pool/pool/website_example
<pre>
&lt;Directory /&gt;
    Options Indexes FollowSymLinks Includes ExecCGI
    AllowOverride All
    Require all granted
    Allow from all
&lt;/Directory&gt;
</pre>

* sudo snap install core; sudo snap refresh core
* sudo snap install --classic certbot
* sudo ln -s /snap/bin/certbot /usr/bin/certbot
* sudo certbot --apache
* sudo nano /etc/apache2/sites-enabled/000-default-le-ssl.conf
* ADD: ( match to your hostname, right before /VirtualHost )
<pre>
ProxyPass /api/ http://pool.bmorecoin.com:8117/
ProxyPassReverse /api/ http://pool.bmorecoin.com:8117/
</pre>
* sudo a2enmod proxy
* sudo a2enmod proxy_http
* sudo a2enmod proxy_balancer
* sudo a2enmod lbmethod_byrequests
* sudo systemctl restart apache2

Old Commands
* curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash

Credits
---------
* [insidenothing](//github.com/insidenothing) - Developer of BMoreCoin & BMoreToken
* [fancoder](//github.com/fancoder) - Developper on cryptonote-universal-pool project from which current project is forked.
* [dvandal](//github.com/dvandal) - Developer of cryptonote-nodejs-pool software

License
-------
Released under the GNU General Public License v2

http://www.gnu.org/licenses/gpl-2.0.html

Digital Ocean
--------------

https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-18-04
