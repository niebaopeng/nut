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
                [--substitute [substitution [substitution ...]]]
                [--dump-path DUMP_PATH]
                [--logging-level {CRITICAL,ERROR,WARNING,INFO,DEBUG,NOTSET}]
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
  --substitute [substitution [substitution ...]], -s [substitution [substitution ...]]
                        Specify regular expression substitutions in the form
                        -s/pattern/replace/ where / can be replaced with
                        another delimiter. The substitution is applied to
                        *every* message. By default no substitutions are
                        performed.
  --dump-path DUMP_PATH, -d DUMP_PATH
                        Specify a path to dump all messages to. Messages are
                        dumped into individual files and organized by
                        destination into paths formatted using the destination
                        information as follows:
                        "{dump_path}/{a|b}/{udp|tcp}{port}". The messages are
                        stored if files named by an increment message count,
                        starting at zero, and written in "%09d" format without
                        extension. Message timestamps are written in ASCII
                        format ("%f") to a file named "timestamps.txt" stored
                        in the aforementioned path. The timestamps are in
                        seconds relative to the first message received amongst
                        *all* relays. This dump can be replayed to
                        reconfigurable destinations using the replay.py
                        script. Note that the message dumped is after all
                        substitutions.
  --logging-level {CRITICAL,ERROR,WARNING,INFO,DEBUG,NOTSET}
                        Sets the logging level. Must be CRITICAL, ERROR,
                        WARNING, INFO, DEBUG, or NOTSET. Default is DEBUG.
```

Replay
-----

Use [`nut/replay.py`](./nut/replay.py) directly or install it and use the
`nutreplay` entry point script.

```
usage: replay.py [-h] [--host-a HOST_A] [--host-b HOST_B] [--timeout TIMEOUT]
                 [--time-factor TIME_FACTOR]
                 [--logging-level {CRITICAL,ERROR,WARNING,INFO,DEBUG,NOTSET}]
                 [endpoint [endpoint ...]]

Replays message dumps made by nutrelay.

positional arguments:
  endpoint              Zero or more endpoint specifications. An endpoint
                        specification is conveniently the same thing as the
                        path to a specific endpoint dump when in a dump
                        folder. The format is "{a|b}/{udp|tcp}{port}".
                        Examples are "b/udp3251" and "a/tcp9001". Since the
                        path to the endpoint dump is valid too a trailing
                        slash is allowed. If no specifications are provided
                        then all endpoints in the current working directory
                        are used. If any specifications are provided then only
                        those are used.

optional arguments:
  -h, --help            show this help message and exit
  --host-a HOST_A, -a HOST_A
                        Host name or IP address for interface A. If not
                        specified then messages meant for host A are not
                        replayed.
  --host-b HOST_B, -b HOST_B
                        Host name or IP address for interface B. If not
                        specified then messages meant for host B are not
                        replayed.
  --timeout TIMEOUT, -t TIMEOUT
                        Sets the socket timeout for TCP connections. Default
                        is 10.0.
  --time-factor TIME_FACTOR, -f TIME_FACTOR
                        Sets the time factor applied to delays between
                        messages. If 0.0 then playback occurs as fast as
                        possible. If 0.5 then playback occurs at twice as
                        fast. If 1.0 then playback is in real time. If 2.0
                        then playback occurs at half speed. Default is 1.0.
  --logging-level {CRITICAL,ERROR,WARNING,INFO,DEBUG,NOTSET}
                        Sets the logging level. Must be CRITICAL, ERROR,
                        WARNING, INFO, DEBUG, or NOTSET. Default is DEBUG.
```
