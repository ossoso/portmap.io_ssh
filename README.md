portmap.io_ssh
========================

Set up of portmap.io tunnel using OpenSSH

Install and configure OpenSSH
-----------------------------

### Test and configure your SSH server connection

-   Follow instructions for how to set up an SSH server for your version of OpenSSH e.g. <https://wiki.archlinux.org/index.php/Secure_Shell#Installation>.
    -   Start the ssh server.
    -   Test the connection with the command on your host machine with

``` 
ssh <username_at_host>@localhost
```

1.  Recommended for security: Force public key authentication

    On your host machine disable password authentication in `/etc/ssh/sshd_config`. Generate ssh public/private rsa key pair (<https://wiki.archlinux.org/index.php/SSH_keys#Generating_an_SSH_key_pair>). Distribute the public authentication keys to your client systems.

### SSH port and port forwarding settings

You may want to change the port inbound to your SSH host from the default 22 for security reasons or because of your network set up. You can find a list of ports without assignment at <https://www.iana.org/assignments/service-names-port-numbers/>.

The port OpenSSH uses for incoming connections is configured by modifying/adding a line with the port number like in the snippet below. The line is added to /etc/ssh/sshd.config file on your host machine. I will use 10022 as my placeholder inbound port in the rest of these instructions.

``` 
Port 10022
```

Write the port you decide to choose down, as you will need to specify it in the steps that follow.

After you have made changes into the *sshd_config* file you need to (re)start the sshd daemon service. On operating systems using systemd this is done by entering as su `systemctl start sshd`.

You now need to set up port forwarding on your router configuration. Look on your router's manual or manufacturers support page on how to do this. On many routers accessing 192.168.1.1 redirects you to your routers configuration page. You must decide on an access port for your router which will be used for the tunnel between the host pc and portmap. I will be using port 41050 for these instructions.

Set this port as the incoming access port for your host and for protocol TCP. Set your host pc private network IP address (should be static) and the SSH communication port 10022.

Sign up for a portmap.io account
--------------------------------

Sign up and activate your account on <http://portmap.io>. Instructions can be found on <http://portmap.io/support>.

### Create a portmap configuration

1.  Follow the instructions on their web pages. Select SSH as the connection type and generate a rsa key. Copy this key to your host system configuration/identification folder (/home/user/.ssh/).

### Create a mapping rule 41050=&gt;10022

Pick the corresponding configuration protocol (TCP) and set the `Port on Portmap.io`, `Port on your PC` fields to 41050=&gt;10022 correspondingly.

Start the tunnel on your host machine
-------------------------------------

By now you should:

-   have sshd running
-   have the set the pem file from portmap on your host
-   configured your routers port forwarding settings

To create a tunnel you now need to run the following command which can be found on portmap.io on your host computer.

``` 
ssh -i ~/.ssh/<your_portmap_username>.<your_config_name>.pem <your_portmap_username>.<your_config_name>@<your_mappingRule_name>-41050.portmap.io -N -R 41050:localhost:10022
```

you can add option -vv to the comman for debugging purposes

*SSH tunnel extras*
-------------------

You can keep the tunnel up and make the connection more robust by making it a service.

If you simply want the tunnel to remain open you can read on commands `nohup` and `disown` for accomplishing this. Using a terminal multiplexer also allows doing this.

Connecting from your client computer
====================================

From your client computer enter the command

``` 
ssh -p 41050 <username_at_host>@gnomad-41050.portmap.io
```
