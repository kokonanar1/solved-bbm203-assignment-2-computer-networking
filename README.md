Download Link: https://assignmentchef.com/product/solved-bbm203-assignment-2-computer-networking
<br>
Your task is to design a simple version of network communication between peers within a network; that is, to implement a highly simplified computer networking protocol family similar to the Internet protocol suite.

The network protocol stack will consist of four layers each of which will have a different purpose as shown in the Table below:

<table width="0">

 <tbody>

  <tr>

   <td width="177"><strong>Layer</strong></td>

   <td width="410"><strong>Protocol Services</strong></td>

  </tr>

  <tr>

   <td width="177"><strong>Application</strong></td>

   <td width="410">Communication between applications that use the network.</td>

  </tr>

  <tr>

   <td width="177"><strong>Transport</strong></td>

   <td width="410">End-to-end data delivery.</td>

  </tr>

  <tr>

   <td width="177"><strong>Network</strong></td>

   <td width="410">Defines packages and provides routing.</td>

  </tr>

  <tr>

   <td width="177"><strong>Data Link / Physical</strong></td>

   <td width="410">Routines for accessing physical media.</td>

  </tr>

 </tbody>

</table>

The network will have a peer-to-pear model. An example network topology is shown in the figure below.

Figure 1: An example of a network topology

Programming Assignment 2      1 The objective is to enable the peers (we’ll refer to them as clients) to communicate to each other by exchanging messages while abiding by a set of protocols.

<h2>1.1         Basic Layer Services</h2>

<ul>

 <li>A client application will place a request to send a message of an arbitrary length to another client in the network. The Application Layer will be responsible for dividing the message into message chunks of allowable size, since the links in the communication network will have a limited message size that can be delivered in a single frame over the physical media. At this point, the sender’s and the receiver’s IDs will be relevant to identify the communicating parties. Each message chunk will be sent in a separate frame.</li>

 <li>Transport Layer is responsible for appending the correct port numbers to the outgoing frames, based on which application is trying to communicate and whether it is an outgoing or an incoming socket.</li>

 <li>Network Layer needs to append the information regarding the IP addresses of the sender and the receiver to the frame.</li>

 <li>Data Link / Physical Layer will finally append the physical MAC addresses of the clients that are on both ends of the link over which frames will make the next hop.</li>

</ul>

<h2>1.2         A Summary of Network Services</h2>

A summary of network services that you need to implement is as follows:

<ul>

 <li>Enable communication between the clients within a network based on the set of protocols.</li>

 <li>Provide a simple routing of data packets using the clients’ routing tables.</li>

 <li>Enable logging of all network activity.</li>

 <li>Print log reports as necessary.</li>

</ul>

<h2>1.3         Network Protocols</h2>

<h3>1.3.1        Frames as Stacks (Last In First Out)</h3>

Before data packets that carry message fragments can be sent, they will have to be encapsulated into Data Link/Physical layer <strong>frames </strong>which <strong>must be implemented as stacks </strong>of information related to each network layer (e.g. message fragment with sender and receiver IDs for the application layer, the sender and receiver’s IP addresses for network layer, port numbers for transport layer, physical MAC addresses for the physical layer, etc.) as defined by the network protocol stack. A frame structure is shown in the figure below.

Figure 2: An example of a frame structure

<h3>1.3.2        Outgoing and Incoming Queues (First In First Out)</h3>

Each client will have send and receive buffers (<strong>that must be implemented as queues</strong>) for outgoing and incoming messages respectively (see the illustration below). You may assume that the buffer size is infinite; i.e. no packets should ever be dropped because there is not enough space on a queue; rather, the queue will expand as much as necessary to accommodate any frames that need to be placed. You need to dynamically allocate and manage (reallocate or free) memory used by all queues as necessary.

Figure 3: An example of communication between the clients A and B

The figure above illustrates an example communication between a client A and a client B in which A is messaging B. The message is first encapsulated in frames each of which carries a message fragment (when the message size exceeds the maximum allowable message length, one message will be sent in multiple frames). As a frame leaves the sender’s outgoing queue, it is inserted onto the receiver’s incoming queue. Frames must follow the FIFO method (the first message chunk will be sent in the first frame and received in the first frame on the receiver’s side).

<h3>1.3.3        Routing</h3>

When a client receives message frames in its incoming queue, it must check who the intended receiver is. If the frame is addressed to the receiving client, it can proceed to unpack the message chunks as soon as the last message fragment has arrived, and pass them to the application layer based on the port number of its listening socket in order to reassemble the message. However, if the message is intended for someone else, the client must check its routing table to determine the next hop that the frames should take to their destination.

Routing tables of each client will have the following format:

<table width="0">

 <tbody>

  <tr>

   <td width="171"><strong>Intended Destination</strong></td>

   <td width="389"><strong>Neighbor to which the packet should be forwarded</strong></td>

  </tr>

 </tbody>

</table>

Routing tables will have multiple rows, one for each client in the network. For instance, the entry | A | B | means that if a client needs to reach A, it should send the frame to B.

A routing example is illustrated in the figure below. In this scenario, client A is trying to send a message client D. The message is longer than the maximum allowed message length, so it is fragmented into four frames. A checks its routing table entry for D. Since A does not have a direct link to D, the routing table specifies which client is on a path that can eventually reach D, and if such client exists (in this case it is client B), A sends its message to that client for forwarding. If the information about the next hop is missing for some reason, the message will be dropped. When B receives the message intended for D, B must check its routing table to determine the next destination for the message frames. In this case, D is a direct neighbor of B (evident from B’s routing table entry), so B forwards the message to D.

Frames may have to make multiple hops over the network before they reach they final destination. The information about the total number of hops will also need to be saved in the logs.

<h3>1.3.4        Logs</h3>

Every network activity must be logged. Thus, each client will have its own log which will store the information about the sent, received, and forwarded messages. Each log entry should store the following information:

<ul>

 <li>Timestamp: the date and time of the activity,</li>

 <li>Message that was carried in the frames,</li>

 <li>Total number of frames that carried the whole message,</li>

</ul>

Figure 4: A routing example

<ul>

 <li>Total number of hops that frames have made so far through the network,</li>

 <li>Sender ID,</li>

 <li>Receiver ID,</li>

 <li>Activity type (message sent, received or forwarded), and</li>

 <li>Activity success status (successful or failed).</li>

</ul>

<h1>2           An Example Use Case</h1>

Assume we have a network shown in Fig. 1 in which the maximum message length supported by the links is 20 characters. The routing tables of each client would be as shown below. Also assume that the client IDs, addresses, and the socket port numbers are as follows.

<table width="0">

 <tbody>

  <tr>

   <td width="86"><strong>Client ID</strong></td>

   <td colspan="2" width="149"><strong>Client IP Address</strong></td>

   <td colspan="2" width="171"><strong>Client MAC Address</strong></td>

  </tr>

  <tr>

   <td width="86">A</td>

   <td colspan="2" width="149">1.2.3.4</td>

   <td colspan="2" width="171">AAAAAAAAAA</td>

  </tr>

  <tr>

   <td width="86">B</td>

   <td colspan="2" width="149">4.3.2.1</td>

   <td colspan="2" width="171">BBBBBBBBBB</td>

  </tr>

  <tr>

   <td width="86">C</td>

   <td colspan="2" width="149">8.8.8.8</td>

   <td colspan="2" width="171">CCCCCCCCCC</td>

  </tr>

  <tr>

   <td width="86">D</td>

   <td colspan="2" width="149">9.9.9.9</td>

   <td colspan="2" width="171">DDDDDDDDDD</td>

  </tr>

  <tr>

   <td width="86">E</td>

   <td colspan="2" width="149">0.0.1.1</td>

   <td colspan="2" width="171">EEEEEEEEEE</td>

  </tr>

  <tr>

   <td width="86"> </td>

   <td width="119"><strong>Service</strong></td>

   <td colspan="2" width="115"><strong>Port Number</strong></td>

   <td width="86"> </td>

  </tr>

  <tr>

   <td width="86"> </td>

   <td width="119">Sending socket</td>

   <td colspan="2" width="115">0706</td>

   <td width="86"> </td>

  </tr>

  <tr>

   <td width="86"> </td>

   <td width="119">Listening socket</td>

   <td colspan="2" width="115">0607</td>

   <td width="86"> </td>

  </tr>

  <tr>

   <td width="86"></td>

   <td width="119"></td>

   <td width="30"></td>

   <td width="85"></td>

   <td width="86"></td>

  </tr>

 </tbody>

</table>

Figure 5: A demonstration of routing scenario

Let’s suppose that client C wants to communicate with client E and send the message with the following content: “<strong>A few small hops for frames, but a giant leap for this message.</strong>” Since the length of the whole message is 63 characters and the maximum supported frame message length is 20, the message will have to be sent over four different frames with the following message chunks (note that spaces also count as characters):

<ol>

 <li>“A few small hops for”</li>

 <li>“ frames, but a giant”</li>

 <li>“ leap for this messa”</li>

 <li>“ge.”</li>

</ol>

The following steps will need to occur within the network for this communication to happen:

<ol>

 <li>C will fragment the message and encapsulate it within four frames to be sent, and place the frames in its outgoing queue. For example, the first frame would have the following format:</li>

</ol>

The remaining three frames will be similar, with the only difference in the message chunk they carry. After this process, the outgoing queue of client C will look as shown below.

<ol start="2">

 <li>C checks its routing table to determine the next receiver. From C’s routing table it is evident that the next hop for packets intended for E should be client B, since C is not E’s direct neighbor. Therefore, C will send the frames to B, adjusting the Physical layer information (MAC addresses) accordingly, to reflect the fact that the frames will make the next hop over the C-B link.</li>

 <li>B will receive the frames in its incoming queue in the same order they are removed from C’s outgoing queue. This process must be performed using queue operations; i.e. as a frame is removed from the sender’s outgoing queue, it will be inserted onto the receiver’s incoming queue, until all frames have been transferred.</li>

 <li>B will inspect the receiver information within the frames to check the intended receiver. It will see that the message is intended for another client in the network, namely E. So, B will remove the frames from its incoming queue and place them on its outgoing queue for forwarding.</li>

 <li>B will check its routing table to determine the next hop the frames should take, and see that the frames should be forwarded to D. At this point, MAC addresses of the frames should again be adjusted accordingly to reflect the fact that the frames will make the next hop over the B-D link. Physical layer of each frame becomes:</li>

 <li>Similarly to the previous receiving case, D will receive the frames in its incoming queue in the same order as they are removed from B’s outgoing queue.</li>

 <li>D will inspect the receiver information within the frames to check the intended receiver. It will see that the message is intended for E, so D will remove the frames from its incoming queue and place them on its outgoing queue for forwarding.</li>

 <li>D will check its routing table to determine the next hop the frames should take, and see that the E is its direct neighbor. Hence, D will forward the frames to E in a similar manner (again adjusting MAC addresses as necessary).</li>

 <li>Finally, when E receives the frames in its incoming queue, it will notice that the message has reached its intended destination. E will unpack the message and pass it to its application.</li>

</ol>

Note that each activity whether it is sending, reception or forwarding must be logged by the corresponding client in the format described above.

To sum up, in case of a reception of frames, clients need to first check the intended receiver. If the message has reached its destination it can be unpacked, otherwise it should be forwarded. On the other hand, in case of sending, routing tables need to be consulted to determine the next hop for the frames.

<h1>3           Input Files and Parameters</h1>

There will be three input files (two for initializing the network and one with the commands), and three additional arguments specifying the maximum supported message size, outgoing and incoming port numbers for applications that will communicate respectively.

Clients and the network topology will be initialized through the first two input files.

<h2>3.1         Client Info File Format</h2>

number_of_clients Client_ID&lt;space&gt;Client_IP_Address&lt;space&gt;Client_MAC_Address

For example, to initialize a network with five clients from our example scenario the input file will have the following content:

5

<ul>

 <li>2.3.4 AAAAAAAAAA</li>

 <li>3.2.1 BBBBBBBBBB</li>

 <li>8.8.8 CCCCCCCCCC</li>

 <li>9.9.9 DDDDDDDDDD</li>

 <li>0.1.1 EEEEEEEEEE</li>

</ul>

<h2>3.2         Routing Tables File Format</h2>

Client_1_routing_table

<ul>

 <li>Client_2_routing_table</li>

 <li>…</li>

</ul>

–

Client_n_routing_table

Where each client routing table has the following format:

Destination_Client_ID&lt;space&gt;Client_ID_of_nearest_neighbor

For example, our sample use case scenario would have the following routing info file contents:

<ul>

 <li>B</li>

 <li>C</li>

 <li>B</li>

 <li>B</li>

</ul>

–

A A

<ul>

 <li>C</li>

 <li>D</li>

 <li>D</li>

</ul>

–

<ul>

 <li>A</li>

 <li>B</li>

 <li>B</li>

 <li>B</li>

</ul>

–

<ul>

 <li>B</li>

 <li>B</li>

 <li>B</li>

</ul>

E E

–

<ul>

 <li>D</li>

 <li>D</li>

 <li>D</li>

 <li>D</li>

</ul>

<h2>3.3         Commands File Format</h2>

Your works will be tested through the commands that will be given in the third input file. The commands will specify network activity your program must simulate (e.g. which client wants to communicate with whom and what message needs to be sent, what output is expected regarding network status, etc.). The file format is given below.

number_of_commands COMMAND&lt;space&gt;command_parameter_1&lt;space&gt;command_parameter_2…

For a sample command file and the instructions how to execute commands, check the following section Network Status Check Commands.

<h2>3.4         Program Execution</h2>

When your program is executed, all of these parameters will be given as command-line arguments in the following order:

.HUBBMNET clients.dat routing.dat commands.dat max_msg_size outgoing_port incoming_port

Note that file names may change, but the order will always stay the same. Your program must be able to accommodate this.

<h1>4           Network Status Check Commands</h1>

If we were to request a network status check in the example use case discussed above, here is a sample command input file that would accomplish that, and the expected output.

commands.dat content: —————————

7

MESSAGE C E #A few small hops for frames, but a giant leap for this message.#

SHOW_FRAME_INFO C out 3

SHOW_Q_INFO C out

SHOW_Q_INFO C in

SHOW_FRAME_INFO C in 5

SEND C

PRINT_LOG D

Explanation of the commands is given below.

<h2>4.1         Client Communication</h2>

Suppose we want to simulate a message exchange between two clients. MESSAGE command will accomplish the preparation of the message for transmission; i.e. its fragmentation into frames. The format of MESSAGE command is:

MESSAGE&lt;space&gt;sender_ID&lt;space&gt;receiver_ID&lt;space&gt;#message#

This command will trigger the preparation of the message (given between two hash tags which should not be included in the message) for transmission by fragmenting it if necessary, packing it into frames, and placing the frames onto the sender’s outgoing queue. The message should not be actually sent until the command SEND is received.

The output of our sample MESSAGE command should display the information about the message and all frames that were prepared for its transmission in the format given below.

—————————————————————————————

Command: MESSAGE C E #A few small hops for frames, but a giant leap for this message.# ————————————————————————————–Message to be sent: A few small hops for frames, but a giant leap for this message.

Frame #1

Sender MAC address: CCCCCCCCCC, Receiver MAC address: BBBBBBBBBB

Sender IP address: 8.8.8.8, Receiver IP address: 0.0.1.1

Sender port number: 0706, Receiver port number: 0607

Sender ID: C, Receiver ID: E

Message chunk carried: A few small hops for

——–

Frame #2

Sender MAC address: CCCCCCCCCC, Receiver MAC address: BBBBBBBBBB

Sender IP address: 8.8.8.8, Receiver IP address: 0.0.1.1

Sender port number: 0706, Receiver port number: 0607

Sender ID: C, Receiver ID: E

Message chunk carried: frames, but a giant

——–

Frame #3

Sender MAC address: CCCCCCCCCC, Receiver MAC address: BBBBBBBBBB

Sender IP address: 8.8.8.8, Receiver IP address: 0.0.1.1

Sender port number: 0706, Receiver port number: 0607

Sender ID: C, Receiver ID: E

Message chunk carried: leap for this messa

——–

Frame #4

Sender MAC address: CCCCCCCCCC, Receiver MAC address: BBBBBBBBBB

Sender IP address: 8.8.8.8, Receiver IP address: 0.0.1.1

Sender port number: 0706, Receiver port number: 0607

Sender ID: C, Receiver ID: E Message chunk carried: ge. ——–

Note that the receiver information in the Data Link/Physical layer (MAC address) does not match the receiver IP address and ID. The reason for this is the fact that the intended receiver of this message is client E. However, client E is not a direct neighbor of client C, so C must send this message to one of its direct neighbors which can eventually deliver the message to its final destination. This information is provided in C’s routing table. C must check its routing table to see who is on the other side of the link that can deliver the message to E. In our sample use case, that next hop neighbor is client B; therefore, the Data Link/Physical layer information must be adjusted accordingly. Even though the final destination for the message is client E, message needs to take a next hop in the network over the C-B link, and this layer does not care about IP addresses or clients’ IDs. It only cares about the MAC addresses of clients on both ends of the link over which frames will travel at that point.

<h2>4.2         Incoming/Outgoing Queue Status</h2>

Suppose we requested to inspect the contents of the Frame #3 on the C’s Outgoing Queue just as the communicating application placed the request for sending the message. Command SHOWFRAMEINFO will accomplish that. Its format is:

SHOW_FRAME_INFO&lt;space&gt;client_ID&lt;space&gt;which_queue&lt;space&gt;frame_number

The parameter which queue can be either “in” or “out” specifying the outgoing and incoming queue respectively. The information that needs to be displayed when this command is placed is as follows:

———————————

Command: SHOW_FRAME_INFO C out 3

———————————

Current Frame #3 on the outgoing queue of client C

Carried Message: ” leap for this messa”

Layer 0 info: Sender ID: C, Receiver ID: E

Layer 1 info: Sender port number: 0706, Receiver port number: 0607

Layer 2 info: Sender IP address: 8.8.8.8, Receiver IP address: 0.0.1.1

Layer 3 info: Sender MAC address: CCCCCCCCCC, Receiver MAC address: BBBBBBBBBB

Number of hops so far: 0

SHOWQINFO command is used to inspect a client’s queue in general. Its format is:

SHOW_Q_INFO&lt;space&gt;client_ID&lt;space&gt;which_queue

In our example, we want to inspect the C’s outgoing queue status with the third command. The required output should be as follows:

—————————

Command: SHOW_Q_INFO C out

————————–Client C Outgoing Queue Status

Current total number of frames: 4

Similarly, with the fourth command, the status of C’s incoming queue would be displayed (note that C has not received anything at that point):

————————–

Command: SHOW_Q_INFO C in

————————-Client C Incoming Queue Status

Current total number of frames: 0

With the fifth command we requested to inspect the contents of the Frame #5 on the C’s incoming queue. The output should be:

——————————–

Command: SHOW_FRAME_INFO C in 5 ——————————-No such frame.

<h2>4.3         Sending Messages</h2>

The command SEND will cause the message that is currently placed on the sender’s outgoing queue to be sent to it’s intended receiver. Its format is:

SEND&lt;space&gt;client_ID

This command will trigger transmission and forwarding of the frames from the sender’s outgoing queue until they reach their final destination, as explained in the sample use case scenario. The following network trace output should be produced:

—————-

Command: SEND C

—————-

A message received by client B, but intended for client E. Forwarding…

Frame #1 MAC address change: New sender MAC BBBBBBBBBB, new receiver MAC DDDDDDDDDD

Frame #2 MAC address change: New sender MAC BBBBBBBBBB, new receiver MAC DDDDDDDDDD

Frame #3 MAC address change: New sender MAC BBBBBBBBBB, new receiver MAC DDDDDDDDDD

Frame #4 MAC address change: New sender MAC BBBBBBBBBB, new receiver MAC DDDDDDDDDD A message received by client D, but intended for client E. Forwarding…

Frame #1 MAC address change: New sender MAC DDDDDDDDDD, new receiver MAC EEEEEEEEEE

Frame #2 MAC address change: New sender MAC DDDDDDDDDD, new receiver MAC EEEEEEEEEE

Frame #3 MAC address change: New sender MAC DDDDDDDDDD, new receiver MAC EEEEEEEEEE

Frame #4 MAC address change: New sender MAC DDDDDDDDDD, new receiver MAC EEEEEEEEEE A message received by client E from client C after a total of 3 hops.

Message: A few small hops for frames, but a giant leap for this message.

The most important thing in this step is setting the proper MAC addresses within the frames as they hop over each link. When the frames in our example use case started their journey from client C to client E, their first hop was over the C-B link. For this reason, the MAC addresses of the sender and receiver where CCCCCCCCCC and BBBBBBBBBB respectively. When B receives the frames, sees that they are actually intended for client E, and forwards them to client D, it first must unpack the frames, pop the the Data Link/Physical Layer from each frame, change the MAC address information to reflect the next hop over B-D link, by setting the MAC addresses of the sender and receiver of each frame to BBBBBBBBBB and DDDDDDDDDD respectively, and finally push the changed Data Link/Physical Layer back onto each frame.

Forwarding message to its intended destination may not be always possible. Suppose that in our example use case the routing table of D was as follows:

<ul>

 <li>B</li>

 <li>B</li>

 <li>B</li>

</ul>

E –

That is, when the message intended for client E reaches client D, D would not be able to find what the next hop should be. In that case an error message stating that the user is unreachable should also be displayed. The complete output of the SEND command in such case should be as follows:

—————-

Command: SEND C

—————-

A message received by client B, but intended for client E. Forwarding…

Frame #1 MAC address change: New sender MAC BBBBBBBBBB, new receiver MAC DDDDDDDDDD

Frame #2 MAC address change: New sender MAC BBBBBBBBBB, new receiver MAC DDDDDDDDDD

Frame #3 MAC address change: New sender MAC BBBBBBBBBB, new receiver MAC DDDDDDDDDD

Frame #4 MAC address change: New sender MAC BBBBBBBBBB, new receiver MAC DDDDDDDDDD A message received by client D, but intended for client E. Forwarding…

Error: Unreachable destination. Packets are dropped after 2 hops!

The process of sending messages from the sender client until they reach the final destination (intended receiver) should be performed recursively. The message frames should travel through the network until they reach the intended client, or are dropped due to some unforeseen circumstances (e.g. unreachable user).

<h2>4.4         Client Logs</h2>

With the last command PRINTLOG we requested to print the log of Client D upon the successful completion of communicating the message between C and E. The format of this command is:

PRINT_LOG&lt;space&gt;client_ID

Until that point, D has received and forwarded one message, so the output should be:

———————

Command: PRINT_LOG D ——————–Client D Logs:

————-Log Entry #1:

Timestamp: 2018-9-2 15:59:16

Message: A few small hops for frames, but a giant leap for this message.

Number of frames: 4

Number of hops: 2

Sender ID: C

Receiver ID: E

Activity: Message Received

Success: Yes

————-Log Entry #2:

Timestamp: 2018-9-2 15:59:17

Message: A few small hops for frames, but a giant leap for this message.

Number of frames: 4

Number of hops: 2

Sender ID: C

Receiver ID: E

Activity: Message Forwarded

Success: Yes

In the case where the frames were dropped because the next hop could not be determined due to the lack of routing information, the second log entry of client D would be:

Log Entry #2:

Timestamp: 2018-9-7 11:57:22

Message: A few small hops for frames, but a giant leap for this message.

Number of frames: 4

Number of hops: 2

Sender ID: C

Receiver ID: E

Activity: Message Forwarded Success: No

In case the client does not have any log entries yet, nothing should be printed.

<h2>4.5         Invalid Commands</h2>

In case an invalid command is provided, your program should output the following message:

———————

Command: ORDER pizza ——————–Invalid command.