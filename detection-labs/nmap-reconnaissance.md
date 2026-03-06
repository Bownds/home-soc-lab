# Lab: Network Reconnaissance with Nmap

## Objective

Identify live hosts and open services on the target system using network reconnaissance techniques.

## Attacker

Kali Linux VM

## Target

Ubuntu Server VM

IP: 192.168.86.247

## Tools Used

Nmap

## Commands Executed

Host discovery scan:

nmap -sn 192.168.86.0/24

Service scan of target host:

nmap 192.168.86.247

## Results

The attacker successfully identified the Ubuntu target system on the network and discovered the following open ports:

22/tcp  open  ssh  
80/tcp  open  http  

These services indicate that the target system allows remote login via SSH and hosts a web server.

## Conclusion

Network reconnaissance revealed accessible services on the target host. These services could potentially be used as entry points for further attacks such as credential brute-force attempts or web vulnerability exploitation.
