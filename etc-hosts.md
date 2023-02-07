> Was trying to configure a domain to appear localhost by making changes in /etc/hosts

First, we can take multiple entries in this file.

Ex.
127.0.0.1 localhost
127.0.0.1 stage.domain.com

This won't reflect the changes. We need to restart the networking service.

Different distros, different methods(ig). For pop, I had to run `/etc/init.d/networking restart`.

## Alias:

I can even alias a domain name to a word

Ex.
192.168.0.8 mail.google.com mail

Now, after restarting the networking service, I can `curl mail`(doesn't really work in browsers since typing mail redirects us to google search).

## Structure:

IP Domain Alias

## Purpose:

`/etc/hosts` serves as a local DNS system for the system.

> "As your machine gets started, it will need to know the mapping of some hostnames to IP addresses before DNS can be referenced. This mapping is kept in the /etc/hosts file. In the absence of a name server, any network program on your system consults this file to determine the IP address that corresponds to a host name."

## Links:

- More about /etc/hosts: https://unix.stackexchange.com/questions/421491/what-is-the-purpose-of-etc-hosts
- More about the networking service using systemd: https://www.layerstack.com/resources/tutorials/How-to-restart-Network-Interface-or-Network-Adapter-on-Linux-and-Windows-Cloud-Servers
