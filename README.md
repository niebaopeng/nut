Network Utility... Things
=========================

Intended to be a place for various Python based network utilities. Currently
only contains a UDP/TCP port relay. Next addition will probably be a UDP/TCP
"tee" or broadcast.

Installation
------------

The scripts can be used standalone or installed.

```
pip install nut
```

or

```
git clone https://github.com/vietjtnguyen/nut.git
cd nut
python setup.py install
```

Tools
=====

Relay
-----

Use [`nut/relay.py`](./nut/relay.py) directly or install it and use the
`nutrelay` entry point script.

```
usage: relay.py [-h] [--udp [udp_port_spec [udp_port_spec ...]]]
                [--tcp [tcp_port_spec [tcp_port_spec ...]]]
                [--max-message-size MAX_MESSAGE_SIZE]
                [--tcp-connection-backlog BACKLOG]
                host_a host_b

Acts as a TCP and UDP relay from one interface to another. This means any
message received on this machine on one of the ports specified from one of the
hosts is relayed to the other host across the same protocol and same port (or
other port if specified). Handy for, say, tunneling some networking out of a
VM to an external network.

positional arguments:
  host_a                Host name or IP address for interface A.
  host_b                Host name or IP address for interface B.

optional arguments:
  -h, --help            show this help message and exit
  --udp [udp_port_spec [udp_port_spec ...]], -u [udp_port_spec [udp_port_spec ...]]
                        UDP port numbers to relay. If just an integer then the
                        same port is used on each interface. If in the format
                        "####-####" (or a regex of "[0-9]+-[0-9]+") then it is
                        interpreted as a port remapping where the first
                        integer (before the dash) is the port to listen and
                        send to for host A and the second integer (after the
                        dash) is the port to listen and send to for host B.
  --tcp [tcp_port_spec [tcp_port_spec ...]], -t [tcp_port_spec [tcp_port_spec ...]]
                        TCP port numbers to relay. If just an integer then the
                        same port is used on each interface. If in the format
                        "####-####" (or a regex of "[0-9]+-[0-9]+") then it is
                        interpreted as a port remapping where the first
                        integer (before the dash) is the port to listen and
                        send to for host A and the second integer (after the
                        dash) is the port to listen and send to for host B.
  --max-message-size MAX_MESSAGE_SIZE, -m MAX_MESSAGE_SIZE
                        Sets the max message size in bytes (default=16384)
  --tcp-connection-backlog BACKLOG, -b BACKLOG
                        Sets the backlog size for TCP connections (default=5)
```
