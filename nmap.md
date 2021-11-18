
# NMAP

Nmap is a footprinting tool that gets more information about the target (IP, website, etc.).
Nmap is a noisy scanner, so a firewall or a server will recognize it.
Also you are __NOT__ anonymous.

Nmap has a dedicated [site](http://scanme.nmap.org/) to test some stuff.



## Look up IP addresses

To see IP adresses grouped by country, use [nirsoft](https://www.nirsoft.net/countryip/).

If you have an IP address, search for it with `whois <IP address>` in your search engine of choice.
Some sites are [geoiplookup](http://geoiplookup.net) or [ipinfo.io](http://geoiplookup.net).

To find out IP addresses for domains, or vice versa, use `nslookup`.

```sh
nslookup <IP or domain> # or
dig <IP or domain>
```

Ofc, these scan results can be saved in a log file:

```sh
nslookup scanme.nmap.org >> log.txt
```

## Using NMAP

### Scan for open ports

Using the nmap command without parameters scans 1000 ports whether they are open.

```sh
nmap scanme.nmap.org
```
The result will look something like this:

```sh
Starting Nmap 7.60 ( https://nmap.org ) at 2020-03-31 17:09 CEST
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.17s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 996 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
9929/tcp  open  nping-echo
31337/tcp open  Elite

Nmap done: 1 IP address (1 host up) scanned in 16.46 seconds
```

It lists all open ports, and how many ports are closed. It also provides some additional information, like latency and alternative (IPv6) addresses.

If a port is displayed as `filtered`, nmap can't really tell, if the port is open or not. So avoid these if possible.

This is not very good practice though, as this takes a lot of time (esp. for large sites).
Also it's advisable to save all scans in a file, so you can access them later, even after closing the terminal.

### Scanning an address range

Nmap can scan a range of IP addresses for open ports:

```sh
nmap -oG -192.168.1.0-255 -vv > ~/scan.txt
```

This scans the whole subnet and outputs extra information with the `-vv` parameter (double verbose).
However, this method still takes a long time and it is better to target specific ports.
To do so, just add the `-p` parameter.

```sh
nmap -oG -192.168.1.0-255 -p 22 -vv > ~/scan.txt
```

## Aggressive Scans

These allow for deeper scans and allow more information about the target.
The `-A` (Aggressive) parameter scans for additional information, like the OS, OS version, scripts, a traceroute and so on.

```sh
nmap -A scanme.nmap.org
```

To scan for services and their version, use `-sV`. If one of these services have known vulnerabilities, you can abuse them.

```sh
nmap -sV scanme.nmap.org
```

To speed up the scan, use the `-F` (fast) flag. This only scans for the most used ports, like `http`, `ssh` or `mysql`.

```sh
nmap -F scanme.nmap.org
```

To scan multiple addresses at once, just specify them one by one.

```sh
nmap -F scanme.nmap.org google.com > ~/log.txt
```

The output is formatted really well in aeasy to read manner. This also works with mutliple IP addresses as well as a mic of both domains and IPs.

To save even more time, just scan for open ports with the `--open` flag.

```sh
nmap --open scanme.nmap.org
```

# NMAP Scripting Engine (NSE)

It is used to create and share scripts that automate networks scans and vulnerability analysis.
The nmap scripts are stored in `/usr/share/nmap/scripts/`.

To lost a specific sproct, just use `ls` and `grep`:

```sh
ls -l /usr/share/nmap/scripts | grep ssh
```

To use a acript, specify it with the `--script` flag.

```sh
nmap --script=ssh-hostkey 192.168.1.1
```

To use all scripts available for all open ports, use the `-sC` flag.
This runs all scripts mathing all open ports and enumerates the results.

```sh
nmap -sC 192.168.1.1
```

To brute force the ssh port, use the `ssh-brute.nse` script. This has its own password library, but you can provide your own.

```sh
nmap --script=ssh-brute # or
nmap --script=ssh-brute --script-args userdb=users.txt passdb=passwords.txt
```

Generally, its is advisable to run all scripts for all open ports with `-sC` and then proceed with port specific scripts if some interesing information is found.
