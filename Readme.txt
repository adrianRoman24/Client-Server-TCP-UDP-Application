General description:

The application consists in implementing a server-client application where
customers are subscribers who can be subscribed to different channels. evidence
these subscriptions is held by the server, which manages the tcp clients
(subscribers) and udp clients (those who send messages on different channels). a
customer can subscribe or unsubscribe to non-existent topics without causing errors.
If a customer is subscribed to a topic then he will receive the messages immediately
sent to subscribers of that topic if it is online. If it is
offline will receive all messages on that channel if you opt for
the store & forward option once it is online.

Comments on impressions:

If running executables (server and subscriber) is done with arguments
erroneous then the program will stop and will display an error message. Same thing
it happens if one of them is not sent or not received
correct number of bytes. If a subscriber gives a correct subscribe command or
unsubscribe will display the message requested in the statement in the corresponding console
server after the command has reached the server. When a subscriber connects
with the available id the message requested in the statement will be displayed, otherwise a message will be displayed
which suggests that the id is already in use. All will be displayed in the subscriber
messages received from the server (even from the TCP client) in the required format
regardless of whether they were sent while the customer was online or how long
time the client was offline, but he was subscribed to the respective topic and had store &
active forward. The cases in which it is not possible to open a socket or not
manages to make bind on it are treated with error messages and
closing the application.

Driving directions and commands:
./server <PORT_SERVER>
./subscriber <ID> 127.0.0.1 <PORT_SERVER>
Subscribe: subscribe <topic> <sf> ENTER
Unsubscribe: unsubscribe <topic> ENTER
Exit: exit ENTER (both in server and subscriber)
Where: sf = 1 or 0

sources:

server.cpp

Initially, sockets are created for udp and tcp, they are bound,
file-descriptors tcp, udp and stdin (0) are added to the set of descriptors.
Then, it enters the while loop from which it exits only if the command has been received
"Exit" of the keyboard. Here I go through with a forum all the file-descriptors that are
find out in the multitude of tabs-descriptors and treat each case separately.
The first case is managing a new tcp client. I check in first
the crowd of customers (list) if he is already there (if he is I change his condition
online) and add it to the <socket, client> map that keeps track of customers
online. Then, check if it has messages waiting on any topic (pending)
and I send him all the messages he finds.
The second case is managing a udp client. Here I process the message
received in the buffer using the udp_message structure and send the processed message
according to the statement of all online customers subscribing to the corresponding topic.
The third case is receiving a command from the keyboard. one
valid command is "exit". Any other command is treated with a message
erroneous command.
The last case consists in receiving an order from a tcp client
(subscriber) online. The only valid commands are subscribe or unsubscribe.
In the case of a subscribe, the topic on which the subscribe was made is added in
the list of topics of a client (in the client structure) with the corresponding sf,
and otherwise it is deleted from the list.

subscriber.cpp

Here only the socket that communicates with the server opens and is
connects to the server with the connect function. The only file descriptors
are: the one corresponding to the stdin and the one corresponding to the connection socket
with the server. In the infinite loop it is checked if an order has been received from
keyboard or a message from the server. The exit command is the only one accepted by
the keyboard. From the server you can receive the exit command only if
the subscriber tried to authenticate with an id already existing in the server.
The messages coming from the server are the ones that need to be displayed and are the ones
received from UDP clients for a single topic.

helpers.cpp

This file contains the structures used for client processing,
subscribe messages and messages from UDP clients and
functions used to modularize the server.cpp file. customer
will keep id static, a boolean variable to know if it is online or
no, a list of pending messages that will be sent to the client when it
enter the server (if these messages exist, of course) and a list
topic-sf form to know what topics a subscriber is subscribed to and if
it also wants to receive messages if it is disconnected from the server.
The functions have suggestive names and are used to decode udp and
tcp, for deleting or adding items from topic lists or
messages waiting.