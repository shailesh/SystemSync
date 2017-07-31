# SystemSync

SystemSync is a simple tool to keep folders in sync between machines connected on a network.
Hack this application for SocialCops Backend Developer Challenge. Currently, it supports any number of clients and all of them will be kept in sync. At the core of this application, all files are transferred over the network using 'scp'.

The focus of application's design has been to cover this basic use-case and cover it well. I have tried to taken care of the corner issues that could arise. Some useful features are implemented in this application. For example, you can specify any number of folders to sync.

At this point, this application has many limitations. But, due to the design of this application, I believe it shouldn't require much effort to get rid of these limitations.

1. No versioning: In case of collision, only the latest file will be kept.
2. Only files(and not subfolders) inside this specified folder will be synced


## Running SystemSync

SystemSync is written only for Linux (tested for Ubuntu).

### Dependency

All you need is python installed on your system and you are ready to go. However, there may be some python modules that you need to install.

External Python Modules:
```
pyinotify
to-install: sudo apt-get install python-pyinotify
```

#### Generating public ssh key

As 'scp' is used to transfer files between server and client, every file transfer will require authentication.
To escape entering password on each time, it's a good idea to generate your public ssh keys and the application will take care of everything.
If you have a file with extension ```.pub``` in ```~/.ssh``` directory, you don't need to do anything.
Otherwise, generate a new SSH key.

For further details:
https://help.github.com/articles/generating-ssh-keys

### Getting Started

1. git clone
```
git clone git@github.com:shaileshgoogler/SystemSync
```

2. Choose machine to act as server and client machines to sync

3. On each client/server machine

    * Setting up configuration file - systemsync.cfg.
    Sample configurations have been provided to set various parameters (username, ip address and port) of server and client machines.
    * execute monitor.py
    ```
    python monitor.py -ip <local ip address> -port <port number> -uname <user name> -role <client or server>
    ```
    ##### Client
    ```
    python monitor.py -ip 188.166.209.132-port 1398 -uname Shailesh -role client
    ```
    ##### Server
    ```
    python monitor.py -ip 188 166.209.132 -port 1397 -uname Vinayak -role server
    ```

#### How SystemSync Works : Application Logic:

![Alt text](https://ibb.co/kX9C0Q, "SystemSync Design")

The application works on simple client/server design concept. Different machines
that are to be kept in sync will behave as clients and, either one of them or a
completely different machine will play the role of a server.
Clients never interact with each directly; every communication is between server
and a client.

The overall working of this application can be divided into two parts:

CLIENT:

* Finding modified files:
Each client runs a separate thread to keep track of all the files that get modified or created.

* Syncing those files:
A separate thread pushes those modifed files to the server using 'scp'.


SERVER:

* Receives modified files from a client and ask other clients to pull that file.


#### Code Structure:
1. systemsync.cfg: Configuration file.

2. node.py contains the base class for Server and Client. server.py and client.py contains Server and Client classes which extend the base class.

3. rpc.py: helper class for rpc functions.

4. persistence.py: Implements persistent data structure using pickle.

5. monitor.py: Main executable file
