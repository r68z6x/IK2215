java cIK2215 Programming 
Assignment Introduction
Voravit Tanyingyong
2024-09-05 1Programming assignment overview • Design and implement a reliable protocol for sending/receiving datagrams
• Guaranteed UDP (GUDP)
• Enabling reliable transport over UDP
• Based on the Go-Back-N (GBN) protocol
• Automatic repeat request (ARQ)
• Use acknowledgements and timeouts 
for reliable transmission
• Sliding window flow control 
• Multiple packets in flight
• Asynchronous communication
• Unlike TCP, GUDP is not connection-oriented
(no connection establishment)
2024-09-05 2
GUDP
Application
UDP
IP Network
Transport
ApplicationReliable data transfer and Go-Back-N • Computer Networking: a Top-Down Approach 
• Chapter 3.4 – 3.4.3
• Slides from the authors
• Slides 4 – 8 and 12 – 14
2024-09-05 3Principles of reliable data transfer • One of the most important challenges in networking
• Characteristics of unreliable channel will determine complexity of reliable data transfer protocol (rdt)
2024-09-05 4Reliable data transfer: interfaces
2024-09-05 5
send
side
receive
side
rdt_send(): called from above, 
(e.g., by app.). Passed data to 
deliver to receiver upper layer
udt_send(): called by rdt,
to transfer packet over 
unreliable channel to receiver
rdt_rcv(): called when packet 
arrives on rcv-side of channel
deliver_data(): called by 
rdt to deliver data to upperPipelined protocols
• Pipelining: sender allows multiple, “in-flight”, yet-to-be-acknowledged pkts
• Range of sequence numbers must be increased
• Buffering at sender and/or receiver
• Two generic forms of pipelined protocols: Go-Back-N, selective repeat
2024-09-05 6Pipelining: increased utilization
2024-09-05 7
first packet bit transmitted, t = 0
sender receiver
RTT 
last bit transmitted, t = L / R
first packet bit arrives
last packet bit arrives, send ACK
ACK arrives, send next 
packet, t = RTT + L / R
last bit of 2nd packet arrives, send ACK
last bit of 3rd packet arrives, send ACK
3-packet pipelining increases
utilization by a factor of 3!
 
U 
sender 
= .0024 
30.008 = 0.00081 3L / R 
RTT + L / R 
= 
L: a packet size (8000 bits for 1000 bytes)
R: transmission rate (109 bps for 1 Gbps)
RTT: round-trip-time (~ 30 ms for speed-of-light )
Usender: fraction of time the sender is busy sending bits into the channel
L/R = 8 us (0.008 ms)Pipelined protocols: overview
Go-Back-N (GBN)
• Sender can have up to N unack’ed packets in 
pipeline
• Receiver only sends cumulative ack
• Doesn’t ack packet if there’s a gap
• Sender has timer for oldest unacked packet
• When timer expires, retransmit all unacked
packets
Selective Repeat
• Sender can have up to N unack’ed packets in 
pipeline
• Receiver sends individual ack for each packet
• Sender maintains timer for each unacked packet
• When timer expires, retransmit only that unacked
packet
2024-09-05 8GBN sliding window protocol
• Understand the details of a basic sliding window protocol
• An ACK is an ACK (and not a NACK)
• The receiver sends an ACK only if it receives the next packet in sequence*
• You cannot use an ACK to tell the sender that a packet has been lost (i.e., no NACK)
• No duplicate ACK detection
• The sender increases the window in accordance with the ACK
• Retransmissions are triggered by timeouts (and nothing else)
• Receiving an ACK with unexpected sequence number does not trigger a retransmission
2024-09-05 9
* There can be a problem in this case. We will this dicuss it later on.Sliding window flow control
2024
-09
-05 10
Sender Receiver
ACK
4
P
0
Window (size 3)
0 1 2 3
 
4
5
6
7
P
1P
2
0 1 2 3
 
4
5
6
7
0 1 2 3
 
4
5
6
7
P
3
0 1 2 3
 
4
5
6
7
0 1 2 3
 
4
5
6
7
0 1 2 3
 
4
5
6
7
0 1 2 3
 
4
5
6
7
0 1 2 3
 
4
5
6
7
ACK
2
ACK
3Problem when receiver only ACKs the next sequence
Problem:
• If the receiver sends an ACK only if it receives the next 
packet in sequence, a deadlock occurs when all ACKs 
(= number of window size) were lost
Solution:
• Receiver must send ACK with the expected sequence 
number when it receives a packet with a different 
sequence number than the expected sequence number
• Sender upon receiving an ACK can assume all packets with 
(ACK sequence number - 1) were received successfully
2024-09-05 11
Sender Receiver
P0
E0
ACK1
X
P1
E1
P2
E2
ACK2
X
ACK3
X
Timeout!
E3
P0
E3
P1
E3
P2
Timeout!
Send deadlock!
Window (size 3)Go-Back-N sender • k-bit seq # in pkt header (range of sequence numbers is [0, 2k - 1])
• “window” of up to N, consecutive unack’ed pkts allowed
• ACK(n): ACKs all pkts up to, including seq # n - “cumulative ACK”
• On receiving ACK(n): move window forward to begin at n+1
• Timer for oldest in-flight pkt
• Timeout(n): retransmit packet n and all higher seq # pkts in window
• TCP implementation sends the next expected sequence in the ACK, i.e., ACK(n) ack’ed all pkts up to n-1
• GUDP implementation will also send the expected sequence in the ACK
2024-09-05 12GBN sender extended FSM*
2024
-09
-05 13
Wait start_timer
udt_send
(sndpkt[base])
udt_send
(sndpkt[base+1])
…
udt_send
(sndpkt[nextseqnum
-1])
timeout
rdt_send(data)
if (nextseqnum =3.5 points
>=5 pointsPart 2: Grading criteria
Score at least 2.5 points from the bold items and 3.5 out of 5 in total
1. Send multiple packets in-flight + check packet content (1)
2. Send and receive files with your code without loss (1)
3. Send one file to other receiver without loss (0.5)
4. Send one file to other receiver with loss (0.5)
5. Receive one file from other sender without loss (0.5)
6. Receive one file from other sender with loss (0.5)
7. Send one file to multiple receivers without loss (0.5)
8. Send one file to multiple receivers with loss (0.5)
9. Send multiple files to other receiver without loss (0.5)
10. Send multiple files to other receiver with loss (0.5)
11. Receive multiple files from other sender without loss (0.5)
12. Receive multiple files from other sender with loss (0.5)
2024-09-05 33
>=2.5 points
>=3.5 pointsPlagiarism*
• Plagiarism in practical work and computing code
• “It is important that students ‘do their own work’ when they write computer code, when document an 
experiment, create a design or answer a mathematical problem. If they do not do these activities 
themselves, yet claim the results as their own, this is plagiarism.”
• Students who, with unauthorized aids or otherwise attempt to mislead the exam or when a student's 
performance is otherwise to be assessed, may lead to disciplinary action.
2024-09-05 34
* More information on KTH webpage about Cheating and plagiarismTesting
• We provide sam代 写IK2215、java
代做程序编程语言ple applications that you can use to run with your GUDP code
• VSFtp.java: A class for a simple file transfer protocol
• VSSend.java: An application for sending files over VSFtp
• VSRecv.java: An application for receiving files over VSFtp
• You are responsible for identifying relevant test cases and performing tests
• Need to complete GUDPSocket.java, SenderThread.java, and ReceiverThread.java
• Think through the protocol carefully and know how it should work exactly
• Think through the dynamic behaviour of the GUDP library
• What happens, and when?
• Define the protocol states and transitions
• 
• If you have question:
• Discussion forum: QA for lab activities
• QA sessions for verbal discussion or additional support
2024-09-05 35Test service on http://ik2215.ssvl.kth.se
• Only accessible within KTH network
• From outside KTH network, you must connect via KTH VPN-service
• Part 1: http://ik2215.ssvl.kth.se/prg1
• Part 2: http://ik2215.ssvl.kth.se/prg2
• You must provide:
• Your KTH account i.e., KTH email without the “@KTH.SE” part
• Your submission file 
• Part 1: GUDPSocket.java
• Part 2: SenderThread.java
• The test runs at 00:00 everyday
• Slow: 6-10 minutes per submission
• Results send to the KTH account you provided
2024-09-05 362024-09-05 37
### TEST6: receive one file from other sender with loss (0.5p)
OK: Your code can receive one file when first BSN is lost
OK: Your code can receive one file when first DATA is lost
OK: Your code can receive one file when first FIN is lost
OK: Your code can receive one file when first ACK is lost
OK: Your code can receive one file with random loss
TEST6: OK 0.5p ### TEST7: send one file to multiple receivers without loss (0.5p)
OK: Your code can send one file to multiple receivers
TEST7: OK 0.5p ### TEST8: send one file to multiple receivers with loss (0.5p)
OK: Your code can send one file to multiple receivers
TEST8: OK 0.5p ### TEST9: send multiple files to other receiver without loss (0.5p)
OK: Your code can send multiple files to other receiver
TEST9: OK 0.5p ### TEST10: send multiple files to other receiver with loss (0.5p)
OK: Your code can send multiple files to other receiver
TEST10: OK 0.5p ### TEST11: receive multiple files from other sender without loss (0.5p)
OK: Your code can receive one file from other sender
TEST11: OK 0.5p ### TEST12: receive multiple files from other sender with loss (0.5p)
OK: Your code can receive one file from other sender
TEST12: OK 0.5p
##########
IMPORTANT: You pass only if scores of TEST1-6 >=3.5 points and TEST1-12 >=5.0 points.
You get the scores only when you pass. Otherwise, you get 0 points
RESULTS: PASS
SCORE: 7.0
##########
OK: Code compiles without error.
### TEST1: Send multiple packets in-flight + check packet content (1.0p)
OK: GUDP version must be 1
OK: First packet is GUDP BSN (type 2)
OK: Sequence number is random and not zero or one
OK: BSN packet contains only GUDP header
OK: GUDP version must be 1
OK: Second packet is GUDP DATA (type 1)
OK: Sequence number should be random and not zero
OK: Second packet has an increment sequence number
OK: data packet seems to contain GUDP header + payload
TEST1: OK 1.0p ### TEST2: send and receive files with your code without loss (1.0p)
OK: Your code can send and receive one file
OK: Your code can send and receive multiple files
TEST2: OK 1.0p ### TEST3: send one file to other receiver without loss (0.5p)
OK: Your code can send one file to other receiver
TEST3: OK 0.5p ### TEST4: send one file to other receiver with loss (0.5p)
OK: Your code can send one file when first BSN is lost
OK: Your code can send one file when first DATA is lost
OK: Your code can send one file when first FIN is lost
OK: Your code can send one file when first ACK is lost
OK: Your code can send one file with random loss
TEST4: OK 0.5p ### TEST5: receive one file from other sender without loss (0.5p)
OK: Your code can receive one file from other sender
TEST5: OK 0.5p
Example test output for Part 12024-09-05 38
### TEST7: send one file to multiple receivers without loss (0.5p)
OK: Your code can send one file to multiple receivers
TEST7: OK 0.5p ### TEST8: send one file to multiple receivers with loss (0.5p)
OK: Your code can send one file to multiple receivers
TEST8: OK 0.5p ### TEST9: send multiple files to other receiver without loss (0.5p)
OK: Your code can send multiple files to other receiver
TEST9: OK 0.5p ### TEST10: send multiple files to other receiver with loss (0.5p)
OK: Your code can send multiple files to other receiver
TEST10: OK 0.5p
TEST11: SKIPPED 0.0p
TEST12: SKIPPED 0.0p
##########
IMPORTANT: You pass only if scores of TEST1-6 >=2.5 points and TEST1-12 >=3.5 points.
You get the scores only when you pass. Otherwise, you get 0 points
RESULTS: PASS
SCORE: 5.0
##########
OK: Code compiles without error.
### TEST1: Send multiple packets in-flight + check packet content (1.0p)
OK: GUDP version must be 1
OK: First packet is GUDP BSN (type 2)
OK: Sequence number is random and not zero or one
OK: BSN packet contains only GUDP header
OK: GUDP version must be 1
OK: Second packet is GUDP DATA (type 1)
OK: Sequence number should be random and not zero
OK: Second packet has an increment sequence number
OK: data packet seems to contain GUDP header + payload
TEST1: OK 1.0p ### TEST2: send and receive files with your code without loss (1.0p)
OK: Your code can send and receive one file
OK: Your code can send and receive multiple files
TEST2: OK 1.0p ### TEST3: send one file to other receiver without loss (0.5p)
OK: Your code can send one file to other receiver
TEST3: OK 0.5p ### TEST4: send one file to other receiver with loss (0.5p)
OK: Your code can send one file when first BSN is lost
OK: Your code can send one file when first DATA is lost
OK: Your code can send one file when first FIN is lost
OK: Your code can send one file when first ACK is lost
OK: Your code can send one file with random loss
TEST4: OK 0.5p
TEST5: SKIPPED 0.0p
TEST6: SKIPPED 0.0p
Example test output for Part 2Useful resources
• Course book: 8th and 7th edition
• Read Chapter 3.4 through Chapter 3.4.3 Go-Back-N (GBN)
• TCP Operational Overview and the TCP Finite State Machine (FSM)
• Producer-consumer in Java: Baeldung, geeksforgeeks
• Java queue implementations: Oracle, Baeldung, geeksforgeeks, 
• Java documentation for different classes:
• DatagramSocket, DatagramPacket, 
• LinkedList, ArrayDeque
• Java wait() and notify() methods
2024-09-05 39What you need to do • Read through relevant materials thoroughly
• Guaranteed UDP (GUDP) page on Canvas
• This programming introduction slides
• Read through the given source code template for the assignment
• Familiarize with java programming
• Plan early and work incrementally
• Submit your code to the test service daily!
2024-09-05 40

         
加QQ：99515681  WX：codinghelp
