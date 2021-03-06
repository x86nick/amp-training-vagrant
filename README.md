# amp-training-vagrant
Vagrant files to bootstrap an AMP training environment consisting of an AMP instance and multiple byon nodes based on Centos.

The Vagrant environment has been verified on OSX 10.11 and Windows 10 using Vagrant 1.8.0+ and VirtualBox 5.0.10+. It is recommended to use the latest version of both Vagrant and Virtualbox.

![Vagrant 1.8.0+](https://img.shields.io/badge/Vagrant-1.8.0%2B-blue.svg) ![Virtualbox 5.0.10+](https://img.shields.io/badge/VirtualBox-5.0.10%2B-blue.svg) ![OSX 10.11](https://img.shields.io/badge/OSX-10.11-blue.svg) ![Windows 10](https://img.shields.io/badge/Windows-10-blue.svg)

## How to use

This assumes you have already installed Virtualbox and Vagrant on your local machine and have external network access to the Ubuntu update repositories and the Cloudsoft Artifactory.

The supplied `servers.yaml` defines both an AMP node (ubuntu) and 3 small BYON nodes (centos), you can ignore the BYON nodes if you only intend to demonstrate using cloud locations.

AMP is deployed with login authentication disabled and persistance enabled by default (mapped through to the `amp-persistance` directory in your local repo).

### Starting AMP Node

1. Clone this repo

    ```
    git clone https://github.com/cloudsoft/amp-training-vagrant.git
    ```

2. Cd into the repo and start the AMP vagrant node

    ```
    cd amp-training-vagrant
    vagrant up amp
    ```

3. Connect to the [AMP Web Console](http://10.10.10.100:8081/)

### Starting BYON Nodes

1. From the repo directory you can start the BYON nodes with. (Note that you can add additional nodes to `servers.yaml` if desired)

    ```
    vagrant up byon1 byon2 byon3 byon4
    ```

2. They will be availble to add to your blueprint with the following location

    ```
    location:
      byon:
        user: vagrant
        password: vagrant
        hosts:
          - 10.10.10.101
          - 10.10.10.102
          - 10.10.10.103
          - 10.10.10.104
    ````

### Terminal Access to AMP Node

Web based terminal access to the AMP VM is available via [shellinabox](https://help.ubuntu.com/community/shellinabox) (user `vagrant`, password `vagrant`):

- http://10.10.10.100:4200/

You may also connect using `vagrant ssh <node name>`, for example:

    vagrant ssh amp

Or by ssh'ing to the VM as the `vagrant` user:

    ssh vagrant@10.10.10.100

See the [IP Addresses section](#IP-Addresses).

### Viewing AMP Logs

AMP and system logs are accessible via the [Tailon](http://tailon.rtfd.org/) webapp:

- http://10.10.10.100:9000/

Select a log to display from the menu bar:

![Tailon log selection](/files/tailon_log_selection.png?raw=true "Tailon log selection")

Tailons configuration can be altered to increase the number of lines displayed by clicking the wrench icon.

![Tailon config](/files/tailon_config.png?raw=true "Tailon config")
 
### Customising Training VMs 
The following optional steps are provided to describe how you may override the default VM configurations.

#### AMP Version
You can either use the default AMP version or override by changing the `AMP_VERSION` field in `servers.yaml`.

#### Running OS Updates
As a convenience you can set `run_os_update` to `true` in the `servers.yaml` file to automatically run package updates for `apt` and `rpm` based distros. This defaults to `false` to minimise the amount of data downloaded.

#### IP Addresses
All nodes will start with a private interface on the 10.10.10.0/24 network. The default IPs assigned to each node are as follows:

| vagrant host | ip address   |
| ------------ | ------------ |
| amp          | 10.10.10.100 |
| byon1        | 10.10.10.101 |
| byon2        | 10.10.10.102 |
| byon3        | 10.10.10.103 |
| byon4        | 10.10.10.104 |

You can override the IP addresses assigned to each node by changing the `ip` for each machine in `servers.yaml`

**NOTE** These private addresses will only be accessible from your local machine. It is possible, but not documented, to expose some service ports via your local machine (reach out if you believe this would be useful for you).

#### VM Resources
You can alter the base OS, number of CPUs and amount of RAM allocated to each VM by altering the `box`, `cpu` or `ram` fields in `servers.yaml`. For example to switch a VM to Ubuntu Trust64 with 3 cpu cores and 1GB of RAM you would change the fields as follows:

```
box: bento/centos-6.7
ram: 1024
cpus: 3
```
