# NAME
`tcpshark` - Packet analyzer for TCP troubleshooting

# SYNOPSIS
### list mode

    tcpshark -r <infile> [-p <port>] [-4 | -6 | -a <addr>] [-H] [-t a|ad|r]
                              [-d] [-z | -f <field>]

### flow mode

    tcpshark -r <infile> -s <index> [-H] [-t a|ad|dd|e|r|rs] [-v] [-w] [-q]
                              [-z] [-o | -x <length> | -f <field>]

Live capture

    tcpshark [-i <interface>] [-p <port>] [-a <addr>] [-H] [-t a|ad|dd|e|r|rs]
                              [-w] [-q] [-z] [-o | -f <field>]

### one mode

    tcpshark -r <infile> -n <number> [-e | -X]

# DESCRIPTION
TcpShark is network analyzing script, powered by Wireshark.  
This utility displays visually TCP stream for ease of analysis.

### How to use
First, lists TCP streams in **list mode**.  
Next, specifies the index of stream and looks over the packet flow in    
**flow mode**. In addition, you can analyze as you like by piping the result  
to `less -R` command.

    $ tcpshark -r tcpdump.pcap -s 0 | less -R

In flow mode for live capture, needs to run as root user.

# OPTIONS
- `-r <infile>`  
Reads packet data from infile.  
This utility can analyze the file captured by Wireshark, tcpdump,  
snoop, etc.  

- `-i <interface>`  
Reads live packet captured from the network interface.  
Network interface should match one of the names or numbers listed in  
`tshark -D` command. If no interface is specified, the first non-  
loopback interface is choosed.

- `-s <index>`  
Displays the specific stream in flow mode.  

- `-n <number>`  
Displays the specific packet in one mode.  

- `-H`  
Omits the header information.  

- `-t a|ad|dd|e|r|rs`  
Selects the format of the packet timestamp.  
The format can be one of:  
  |Type|Description|
  |:--|:--|
  |`a`|absolute time with no date **[Default]**|
  |`ad`|absolute date and time|
  |`dd`|delta time since the previous displayed packet|
  |`e`|epoch time in seconds since Jan 1, 1970 00:00:00|
  |`r`|relative time since the first packet in the capture file|
  |`rs`|relative time since the first packet in the stream|

- `-p <port>`  
Only streams with specified port are displayed.

- `-4`  
Only IPv4 streams are displayed.

- `-6`   
Only IPv6 streams are displayed.

- `-a <addr>`  
Only streams with specified IPv4/v6 address are displayed.

- `-d`  
Streams list is sorted in descending order according to the total  
number of packets.  **(Linux use only)**

- `-v`  
Reverses the source and destination.

- `-w`  
Calculated window size is displayed, if scaling.

- `-q`  
Relative sequence numbers and acknowledgement numbers are displayed.  
In default, absolute numbers.

- `-z`  
TCP analysis information is displayed.  
In detail, see "**OUTPUT FORMAT**".

- `-o`  
TCP Option's values are displayed.

- `-x <length>`  
TCP segment data up to the specified bytes is output by hexadecimal  
dump. **(Wireshark 2.4.0 or newer)**  
Upper limit is 65536. If length is 0, all data is displayed.  
NOTE: this data is TCP payload data, so not include TCP, IP, and  
Ethernet.  

- `-X`  
Packet data is output by hexadecimal dump.

- `-f <field>`  
Specified field is displayed.  
In list mode, statistics information is displayed in each direction.  
In flow mode, can be specified up to twice.  
About available field, see Wireshark web site.  
https://www.wireshark.org/docs/dfref/

- `-e`  
Prints an entire information of the packet details.

- `-h`  
Prints this help page.

- `-V`  
Prints the version and exits.

# OUTPUT FORMAT
Streams or packets are output in the following format:  

### list mode

      Filter             Display filter used by Wireshark.
      
      CField             Custom field for statistics information.
      
      Index              Stream index.
      
      TIME               Timestamp in first packet.
      
      Src/Dst ADDRESS    Source/Destination IP address and port.
                         When IPv6 packets are encapsulated into IPv4 (6to4,
                         6in4, or 6rd), both address pairs of the stream are
                         displayed.
      
      Duration           Period between first/last packets.
      
      FLAGS              TCP flag's bitwize OR on all packets.
      
                           F : Fin
                           S : Syn
                           R : Reset
                           P : Push
                           A : Acknowledgment
                           U : Urgent
                           E : ECN-Echo
                           C : Congestion Window Reduced (CWR)
                           N : Accurate ECN / Nonce
      
      Protocol           Upper layer protocol over TCP/IP.
                         If a stream contains multiple protocols, displays up to
                         three protocols.
      
      Packets Bytes      Number of packets count and total of TCP data length in
                         each direction.
                         Note that data length is not a frame size.
      
      Analysis           TCP analysis statistics.
      
                           ret : Retransmission
                           fst : Fast Retransmission
                           spu : Spurious Retransmission  (Wireshark 1.12.0 or newer)
                           dup : Duplicate ACK
                           out : Out Of Order
                           los : Previous Segment Unseen
                           alo : ACKed Unseen Packet
                           wup : Window update
                           ful : Window full
                           zro : Zero Window
                           zrp : Zero Window Probe
                           zpa : Zero Window Probe Ack
                           kep : Keep Alive
                           kpa : Keep Alive ACK
                           tfo : SYN with TFO cookie      (Wireshark 2.0.0 or newer)
                           afo : Accepting TFO data       (Wireshark 3.4.0 or newer)
                           ifo : Ignoring TFO data        (Wireshark 3.4.0 or newer)
                           prt : TCP Port numbers reused
      
      Custom STATISTICS  Number of packets which includes the specified field.
                         Calculates sum-total, minimum, maximum and average
                         value, if field type is numerical (INT, UINT, BOOLEAN,
                         DOUBLE, FLOAT, or RELATIVE_TIME).

### flow mode

      Filter             Display filter used by Wireshark.
      
      CField             Custom field(s).
      
      Stream             Pair of IP address and TCP port.
                         MAC addresses are displayed if Ethernet. Additionally,
                         vlan id is displayed if IEEE 802.1Q.
      
                         If known vendor, vendor name for oui is displayed.
                         (Wireshark 3.2.0 or newer)
      
      No.                Packet number in capture file.
      
      TIME               Timestamp in each packet.
      
      WINDOW Size        TCP windows size.
      
      Src/Dst PORT       Source or Destination port.
      
      LENGTH             TCP segment length.
      
      FLAGS              TCP flags.
                         Please refer to the description in <list mode>.
      
      SEQ/ACK Number     Sequence or Acknowledgment number.
      
      OPTION             TCP options.
      
                           mwSstO
                           ------
                           |||||+ Other option (except EOL, NOP)
                           ||||+- TCP Time Stamp Option
                           |||+-- TCP SACK Option
                           ||+--- TCP SACK Permitted Option
                           |+---- TCP Window Scale Option
                           +----- TCP MSS Option
      
      Analysis           TCP analysis information for TCP troubleshooting.
                         Please refer to the description in <list mode>.
      
      Protocol           Upper layer protocol over TCP/IP.
      
      Information        Information for upper layer protocol.
                         Same contents as Wireshark 'Info' field is displayed.
      
      Option VALUES      TCP Option's values (if some bits in OPTION area are
                         set).
      
                           [m] MSS=XXX             : MSS value
                           [w] WS=XXX(YYY)         : Shift count, multiplier
                           [S] SACK_PERM=1         : ---
                           [s] SLE=XXX SRE=YYY     : ACK left edge, right edge
                           [t] TSval=XXX TSecr=YYY : Timestamp value, echo reply
                           [O] OTHER(kind:XXX)     : TCP option's kind
      
      Payload DATA       Hexadecimal dump and printable ascii string of TCP
                         segment data.
      
      Custom VALUE       Value of specified custom field.
                         Only displays if each packet includes the field.
                         If you specify -f option twice, 2nd field is displayed
                         in parentheses.

### flow mode
TcpShark displays packet details or hex dump from the following command  
result:

    $ tshark -r <infile> -Y "frame.number == <number>" -V  
    or  
    $ tshark -r <infile> -Y "frame.number == <number>" -x  


# ENVIRONMENT VARIABLES
|Variable|Description|
|:--|:--|
|TCPSHARK_TSHARK_COMMAND|File path of executable command `tshark`.<br>If command not found on your system, set the full path of the command.|
|TCPSHARK_EXECUTION_USER|Execution user name to use instead of root.<br>If you run as root user, TcpShark internally executes tshark as<br>the user specified in this variable.<br>(**Linux, Solaris use only**)|
|TCPSHARK_MAX_STREAMS|Maximum number of streams that can be processed in list mode.<br>Default value is 262144. Upper limit is 1048576.<br>If a huge number of streams in your capture file, set the number<br>of streams or each more.<br>Note that it may require a lot of memory to process huge streams.|
|TCPSHARK_APPEARANCE|TcpShark displays colorfully with ANSI color escape sequences.<br>Selects appearance according to your terminal color from<br>`Mono`(default), `Dark`(recommended) or `Light`.<br>If `Mono`, displays in monochrome.<br>If your terminal's background is blackish color, set `Dark`.<br>If whitish color, set `Light`.|
|TCPSHARK_MAX_INFORMATION_LENGTH|Maximum length of protocol information in flow mode.<br>If an information is longer than this length, the information is<br>truncated to this length.<br>Default length is 48. Upper limit is 65536. If 0, not truncated.|

# EXIT STATUS
This utility exits 0 on success, or 1 if error.

# REQUIREMENTS

### Required Software
- [Wireshark](https://www.wireshark.org)
- [Cygwin](https://www.cygwin.com) 　(Windows only)

### Operating System
- Linux
- Solaris
- macOS
- Windows

FYR, operation has been confirmed on the following platforms:
- Cent OS 7.5, 8.4
- Red Hat Enterprise Linux 7.7, 8.2
- Ubuntu 20.04
- Oracle Solaris 11.4 (Intel)
- macOS 12.0.1 (Arm)
- Cygwin 3.3.3 in Windows 10 21H1

# INSTALLATION

1. Download the script file in **/usr/local/bin** directory.  
   `wget https://raw.githubusercontent.com/manabapp/TcpShark/main/tcpshark`  
   Or  
   `curl -o tcpshark https://raw.githubusercontent.com/manabapp/TcpShark/main/tcpshark`  
2. Gives you permission to execute the file.  
   `chmod 0755 tcpshark` 
4. **(Optional)** Sets the environment variable **TCPSHARK_APPEARANCE** in .bash_profile, .zprofile, etc.  
   (e.g. `export TCPSHARK_APPEARANCE=Dark` if your terminal background color is black)
3. **(Windows Only)** Sets the environment variable **TCPSHARK_TSHARK_COMMAND** in .bash_profile, .zprofile, etc.  
   `export TCPSHARK_TSHARK_COMMAND=<Wireshark install folder>/tshark.exe`  
   (e.g. `export TCPSHARK_TSHARK_COMMAND="/cygdrive/c/Program Files/Wireshark/tshark.exe"`)

# VERSION
The current stable release of TcpShark is 4.5.4 in Oct 10, 2022.  
(md5: 0d45e8da999e3cbb9dca575eda6e1287)

# LICENSE
GPLv3+: GNU GPL version 3 or later <https://www.gnu.org/licenses/gpl.html>

---

Copyright (C) 2014-2022 manabapp.
