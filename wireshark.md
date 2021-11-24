# Wireshark CLI tool

Use `tshark` (Arch) to start the utility.

Flag | Meaning
--- | ---
`tshark -D` | find all network interfaces that wireshark can use
`tshark -i <interface>` | state which interface wireshark will listen to
`tshark -c <number>` | limit the number of captured packets
`tshark tcp` | capture all tcp packets, same for udp
`-x` | display packets in hexadecimal format
`-w /path/to/file` | save output to file in binary format
`-r /path/to/file` | read from file
`-t ad` | add timestaps to each packet
`-V` | view an entire packet

### Capture packets between your machine and a specific host

```sh
tshark -i <interface> host <hostIP>
tshark -i enp14s0 host 192.168.1.1
tshark -i enp14s0 host 192.168.1.1 and port 80
```

### Capture TCP/TLS handshake

Run `tshark` bound to a specific host in one terminal and `wget` to that host in another terminal.
The first packet is a `[SYN]` request from the client and the second packet is the reply from the server with `[SYN, ACK]`.
The third and final packet is the reply from the client with `[ACK]`. Now the TCP handschake is complete and the client sends a HTTP GET request.

The same works for a TLS handshake, expect TLS uses 11 packets.
The first 3 packets are again for the TCP handshake.
