# NAME
`tcpshark` - Packet analyzer for TCP troubleshooting

# SYNOPSIS
### list mode

    tcpshark -r <infile> [-H] [-C] [-t a|ad|r] [-p <port>] [-4 | -6 | -a <addr>]
                         [-d] [-z | -f <field>]

### flow mode

    tcpshark -r <infile> -s <index> [-H] [-C] [-t a|ad|dd|e|r|rs] [-v] [-w] [-q]
                         [-z] [-x <length> | -f <field> | -e rtt|rto|dup]

### one mode

    tcpshark -r <infile> -n <number> [-C]

# DESCRIPTION
TcpShark is network analyzing script, powered by Wireshark.  
This utility displays visually TCP stream for ease of analysis.

### How to use
First, lists TCP streams in **list mode**.
<img width="1076" alt="ScreenShot0" src="https://user-images.githubusercontent.com/73800089/145710097-b8706c0c-ca79-42fa-a517-2bc71d537481.png">

Next, specifies the index of stream and looks over the packet flow in **flow**  
**mode**.
<img width="1076" alt="ScreenShot1" src="https://user-images.githubusercontent.com/73800089/145710099-fac677ae-1c79-48a8-b01e-b58301f9adb9.png">

In addition, you can analyze as you like by piping the result to  
`less -R` command.

    $ tcpshark -r tcpdump.pcap -s 0 | less -R

# OPTIONS
- `-r <infile>`  
Reads packet data from infile.  
This utility can analyze the file captured by Wireshark, tcpdump,  
snoop, etc.  

- `-s <index>`  
Displays the specific stream in flow mode.  

- `-n <number>`  
Displays the specific packet in one mode.  

- `-H`  
Omits the header information.  

- `-C`  
Displays a result in colorless.  
In default, displays colorfully with ANSI color escape sequences.  

- `-t a|ad|dd|e|r|rs`  
Selects the format of the packet timestamp.  
The format can be one of:  
  ||||
  |:--|:--|:--|
  |`a`|absolute time with no date|Default|
  |`ad`|absolute date and time||
  |`dd`|delta time since the previous displayed packet||
  |`e`|epoch time in seconds since Jan 1, 1970 00:00:00||
  |`r`|relative time since the first packet in the capture file||
  |`rs`|relative time since the first packet in the stream||

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

- `-x <length>`  
TCP segment data up to the specified bytes is output by hexadecimal  
dump. If length is 0, all data is displayed.  
NOTE: this data is TCP payload data, so it does not include TCP, IP,  
and Ethernet headers.  

- `-f <field>`  
Specified field is displayed.  
In list mode, statistics information is displayed in each direction.  
In flow mode, can be specified up to twice.  
About available field, see Wireshark web site.  
https://www.wireshark.org/docs/dfref/

- `-e rtt|rto|dup`
Selects the type of TCP expert information.  
The type can be one of:  
  |||
  |:--|:--|
  |`rtt`|RTT to ACK the segment in frame|
  |`rto`|RTO for this segment from frame|
  |`dup`|Duplicate to the ACK in frame, and times|

- `-h`  
Prints this help page.

- `-V`  
Prints the version and exits.

# OUTPUT FORMAT
Streams or packets are output in the following format:  

### list mode

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
                           N : Nonce
      
      Packets Bytes      Number of packets count and total of TCP data length in
                         each direction.
                         Note that data length is not a frame size.
      
      Custom STATISTICS  Number of packets which includes the specified field.
                         Calculates sum-total, minimum, maximum and average
                         value, if field type is numerical (INT, UINT, BOOLEAN,
                         DOUBLE, FLOAT, or RELATIVE_TIME).
      
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

### flow mode

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
      
      Option VALUES      TCP Option's values (if some bits in OPTION area are
                         set).
      
                           [m] MSS=XXX             : MSS value
                           [w] WS=XXX(YYY)         : Shift count, multiplier
                           [S] SACK_PERM=1         : ---
                           [s] SLE=XXX SRE=YYY     : TCP ACK left edge, right edge
                           [t] TSval=XXX TSecr=YYY : Timestamp value, echo reply
                           [O] OTHER(kind:XXX)     : TCP option's kind
      
      Payload DATA       Hexadecimal dump and printable ascii string of TCP
                         segment data.
      
      Custom VALUE       Value of specified custom field.
                         Only displays if each packet includes the field.
                         If you specify -f option twice, 2nd field is displayed
                         in parentheses.
      
      Expert INFO        TCP expert information.
                         Additional information according to the specified type
                         is displayed.

### flow mode
Output of the following command is displayed as is.

    $ tshark -r <infile> -Y "frame.number == <number>" -V
        
# ENVIRONMENT VARIABLES
|Variable|Description|
|:--|:--|
|TCPSHARK_TSHARK_COMMAND|File path of executable command `tshark`.<br>If the command is not found on your lab, set the full path of tshark.|
|TCPSHARK_AWK_COMMAND|File path of executable command `gawk`, `nawk`, or `awk`.<br>If such awk is not found on your lab, set the full path of awk.|
|TCPSHARK_WIRESHARK_OUIFILE|File path of OUI (Organizationally Unique Identifier) list included in<br>Wireshark.<br>If this file is not found on your lab, set the full path of the file.<br>Default path is as follows:<br>&nbsp;&nbsp;&nbsp;`/usr/share/wireshark/manuf` (**Linux**, **Solaris**)<br>&nbsp;&nbsp;&nbsp;`/Applications/Wireshark.app/Contents/Resources/share/wireshark/manuf` (**macOS**)<br>&nbsp;&nbsp;&nbsp;Path of 'manuf' file under the folder installed Wireshark (**Windows**)|
|TCPSHARK_EXECUTION_USER|Execution user name to use instead of root.<br>If you run as root user, TcpShark internally executes tshark as the<br>user specified in this variable.<br>By default, executes tshark as root.|
|TCPSHARK_MAX_STREAMS|Maximum number of streams that can be processed in list mode.<br>Default value is 262144. Upper limit is 1048576.<br>If a huge number of streams in your capture file, set the number of<br>streams or each more.<br>Note that it may require a lot of memory to process huge streams.|
|TCPSHARK_APPEARANCE|TcpShark displays colorfully with ANSI color escape sequences.<br>Selects appearance according to your terminal color from `Dark` or<br>`Light`. Default is `Dark`.<br>If your terminal's background is whitish color, set `Light`.|

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

### Linux, Solaris, macOS
1. Places the script file `tcpshark` in **/usr/local/bin** directory.  
2. Gives you permission to execute the file.  
  `chmod 0755 tcpshark` 
3. **(Optional)** Sets an environment variable in .bash_profile, .zprofile, or etc, if your terminal background color is white.  
  `TCPSHARK_APPEARANCE=Light`

### Windows
1. Places the script file `tcpshark` in **&lt;Cygwin install folder&gt;\usr\local\bin** directory.  
  (e.g. **c:\cygwin\usr\local\bin**)
2. Sets an environment variable in .bash_profile on Cygwin.  
  `PATH=<Wireshark install folder>:${PATH}`  
  (e.g. `PATH="/cygdrive/c/Program Files/Wireshark:${PATH}"`)
3. **(Optional)** Sets an environment variable in .bash_profile on Cygwin, if your terminal background color is white.  
  `TCPSHARK_APPEARANCE=Light`

# LICENSE
GPLv3+: GNU GPL version 3 or later <https://www.gnu.org/licenses/gpl.html>

---

Copyright (C) 2022 manabapp.
