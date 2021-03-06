MASTERNODE BUILD NOTES 
======================
Some notes on how to build a Exvo Masternode in Ubuntu server. Using Windows Qt Client to configure.


System requirements
--------------------
An Ubuntu 16.04 64bit server is recommended with at least 768MB 
of memory and 10GB space available when running a Masternode.


Notes
-----
You need exactly 15000 STAK to run a Masternode. Masternode input must have at least 15 confirmations.
Building a new wallet.dat is also recommended (a seperate .conf is needed). Masternode earnings are
going to this wallet.dat and address. Send only newly earned coins away and pay attention for transaction fees.
To fix a broken accountaddress 0 (possible when sending huge amounts of coins away), do a self TX of 15000 STAK.


Start
-----
Open your Windows Exvo-Qt Client and open the debug console.

    ```
	masternode genkey
	copy the generated code to a text file
	---
	getaccountaddress 0
	copy the generated address to a text file
	---
	encryptwallet "strong password"
    ---
	Send 15000 STAK to "address 0" in a *single transaction*
    ```
	

Build Instructions: Ubuntu & Debian
-----------------------------------

	$ adduser <NEW USER>
	$ passwd <PSW>
	$ gpasswd -a <NEW USER> sudo
	---
    $ sudo apt-get update

    $ sudo apt-get install -y build-essential
    $ sudo apt-get install -y libtool autotools-dev autoconf automake
    $ sudo apt-get install -y libssl-dev
    $ sudo apt-get install -y libboost-all-dev
    $ sudo apt-get install -y pkg-config 

    $ sudo add-apt-repository -y ppa:bitcoin/bitcoin
    $ sudo apt-get -y update
    $ sudo apt-get install -y libdb4.8-dev
    $ sudo apt-get install -y libdb4.8++-dev

    $ sudo apt-get install -y libminiupnpc-dev
    $ sudo apt-get install -y libqt4-dev libprotobuf-dev protobuf-compiler
    $ sudo apt-get install -y libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev
    $ sudo apt-get install -y libcanberra-gtk-module
    $ sudo apt-get install -y gtk2-engines-murrine
    $ sudo apt-get install -y libqrencode-dev
    $ sudo apt-get install -y libevent-dev
    $ sudo apt-get install -y libzmq3-dev


	Swapfile:
	---------
	$ sudo dd if=/dev/zero of=/swapfile bs=2M count=1024
	$ sudo mkswap /swapfile
	$ sudo swapon /swapfile

	
	FireWall:
	---------
	$ sudo apt-get install ufw
	$ sudo ufw allow ssh/tcp
	$ sudo ufw limit ssh/tcp
	$ sudo ufw allow 8585/tcp
	$ sudo ufw logging on
	$ sudo ufw enable
	$ sudo ufw status


	Install STAK
	------------
    # determine latest release tag
    # https://github.com/exvo/exvo/releases/latest
    $ export EXVO_VER=1.14.15.2

	$ mkdir ~/.exvo
	$ cd ~/.exvo
    $ wget https://github.com/exvo/exvo/releases/download/$EXVO_VER/exvo-$EXVO_VER-linux-amd64.tar.gz
	$ tar xvzf exvo-$EXVO_VER-linux-amd64.tar.gz
	
    $ cd exvo-$EXVO_VER-linux-amd64/
	$ sudo cp exvo* /usr/bin
	$ sudo chmod 775 /usr/bin/exvo*

	
	Create a exvo.conf in nano
	-------------------------------
	$ cd ~/.exvo
	$ nano

	---
	rpcuser=<anything>
	rpcpassword=<anything_but_very_secure>
	rpcallowip=127.0.0.1
	maxconnections=100
	listen=1
	server=1
	daemon=1
    promode=1
	masternode=1
	masternodeprivkey=XXXXXX
	externalip=xxx.xxx.xxx.xxx:8585
	---

	save nano: Ctrl +  O
	exit nano: Ctrl +  X
	$ cd


	Create CRON
	-----------
	$ cd /etc/cron.d
	$ crontab -e

	2 (for nano)
	at bottom of newly created file, insert:

	---
	@reboot /usr/bin/exvod -shrinkdebugfile    [to start masternode  (or wherever you keep your daemon)]
	*/20 * * * * /usr/bin/exvod
	---

	save nano: Ctrl +  O
	exit nano: Ctrl +  X
	$ cd
	
	
	Manually start EXVO daemon 
	----------------------------
	$ exvod	
	
	exvo-cli commands
	------------------
    # confirm masternode eligible txn outputs
    $ exvo-cli masternode outputs
	$ exvo-cli masternode start 
	$ exvo-cli masternode stop
    $ exvo-cli masternode current
	$ exvo-cli help
	
	
	Start Mining (optional, but supports network)
	---------------------------------------------
	./exvo-cli generate 100
	./exvo-cli gethashespersec
	

Windows Exvo-Qt Client configuration 
-----------------------------------------
(if using a seperate wallet.dat, a seperate exvo.conf is needed)

	---
	go to exvo.conf in %appdata%
	
	---
	rpcuser=<anything>
	rpcpassword=<anything>
	rpcallowip=127.0.0.1
	listen=1
	maxconnections=100
	masternode=1
    promode=1
	masternodeprivkey=XXXXXX
	externalip=xxx.xxx.xxx.xxx:8585
	---

	
	Windows Exvo-Qt Client console commands
	--------------------------------------------
	masternode start 
	masternode stop
    masternode current
	getinfo
	help
