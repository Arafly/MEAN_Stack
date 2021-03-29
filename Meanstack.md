
In this tutorial we're going to implement a simple Book Register web form using MEAN stack.

## Step 1: Install NodeJs


Update ubuntu

`sudo apt update`

Upgrade ubuntu

`sudo apt upgrade`

Install NodeJS

`sudo apt install -y nodejs`

The "-y" flag is a "yes" to answer all default prompts during the nodejs installation.

Once the installation is done, you can check the Node version with 

`node --version`

```
Output:

v10.19.0
```

## Step 2: Install MongoDB
MongoDB is a non-relational database that stores data in flexible, JSON-like documents. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

```
molokofi@molokofi:~$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
Executing: /tmp/apt-key-gpghome.UzNe71Z8o2/gpg.1.sh --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

gpg: key BC711F9BA15703C6: public key "MongoDB 3.4 Release Signing Key <packaging@mongodb.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1

```

```
molokofi@molokofi:~$ echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse

```

```
molokofi@molokofi:~$ sudo systemctl status mongodb
● mongodb.service - An object/document-oriented database
     Loaded: loaded (/lib/systemd/system/mongodb.service; enabled; vendor preset:>
     Active: active (running) since Mon 2021-03-29 19:06:21 WAT; 5min ago
       Docs: man:mongod(1)
   Main PID: 13723 (mongod)
      Tasks: 23 (limit: 3355)
     Memory: 42.8M
     CGroup: /system.slice/mongodb.service
             └─13723 /usr/bin/mongod --unixSocketPrefix=/run/mongodb --config /et>

Mar 29 19:06:21 molokofi systemd[1]: Started An object/document-oriented database.

```