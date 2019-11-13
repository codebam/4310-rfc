%%%
title = "FooChat 2.0"
abbrev = "FC"
# ipr = "trust200902"
area = "Internet"
workgroup = "Network Working Group"
# submissiontype = "IETF"
keyword = [""]
#date = 1997-04-01T00:00:00Z

[seriesInfo]
name = "RFC"
value = "1407"
stream = "IETF"
status = "experimental"

[[author]]
initials="S. B., K. D. S."
surname="Behan, Dsane-Selby"
fullname="Sean Behan, Kevin Dsane-Selby"
#abbrev = "S & K"
#role = "editor"
#organization = "AT&T Labs Research"
#  [author.address]
#  email = "bellovin@acm.org"
#  street = "180 Park Avenue"
#  city = "Florham Park"
#  code = "NJ 07932"
%%%

.# Abstract

There were not enough chat protocols, FooChat allows seamless communication
between clients and servers. The simplicity of this protocol makes it a good
example for low level networking.

{mainmatter}

# Introduction

Most chat protocols are very complex and require unncessary bandwidth being
used. FooChat solves this problem by implementing a simple protocol for
communication that is also bandwidth efficient.

## Servers

The server of FC, provides a point to which clients may connect to to
communicate with each other.

## Clients

Clients of FC connect to a central server in which they can communicate and
send messages to other clients.

## Terminology 

The keywords **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**,
**SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL**, when they appear in this
document, are to be interpreted as described in [@!RFC2119].

# The FC Specification

## Overview

The FC protocol as described is for use for client to server connections.

## Packet format

Each packet is sent in the JSON format outlined in [@!RFC8259].

The packet structure is as follows.

{align="left"}
	{ 
	  "number": 1,
	  "version": 1,
	  "from": "alice",
	  "to": "bob",
	  "verb" "SEND",
	  "data": "hello bob, how are you?",
	  "checksum": "d4337b8c43aceb0567fbd4ccd09b330727120e0e96856caa6d8da6dba64ba07d"
	}

## Connecting to the server

Clients **MUST** connect to the server using TCP packets. Clients **MUST** also
connect on port **682651**.

To initiate a connection clients **SHALL** send a TCP packet containing the
message LOGN, a username, and a code version number.

The server **SHALL** acknowledge the login by sending a TCP packet containing
SUCC, the user id.

The connection is made as follows:

{align="left"}
	Client -> Server
	{ 
	  "number": 1,
	  "version": 1,
	  "from": "alice",
	  "verb" "LOGN",
	}

	Server -> Client
	{ 
	  "number": 2,
	  "verb" "SUCC",
	}

## Disconnecting from the server

Clients **SHOULD** send a DISC (disconnect) message when disconnecting from the
server.

{align="left"}
	Client -> Server
	{
	  "number": 3,
	  "from": "alice",
	  "verb" "DISC",
	}

## Sending messages

Clients send messages by sending TCP packets with the destination username as
well as the message that you want to send.

Example:

{align="left"}
	Client -> Server
	{
	  "number": 4,
	  "from": "alice",
	  "to": "bob",
	  "verb" "SEND",
	  "data": "hello bob"
	}

## Sending messages to all clients

If messages are sent with a verb of SALL, that message will be broadcast to
all other clients connected to the server.

{align="left"}
	Client -> Server
	{
	  "number": 5,
	  "from": "alice",
	  "verb" "SALL",
	  "data": "message to all clients"
	}

## Receiving messages

Clients implement a receive method such that they are able to recieve and
display incoming messages.

Example:

{align="left"}
	Server -> Client
	{
	  "number": 6,
	  "from": "bob",
	  "verb" "RECV",
	  "data": "good thanks, how are you?"
	}

## Server broadcasts

When a new client connects to the server, the server will broadcast a message
to all clients telling them the username of all connected clients. It is the
clients job to keep track of connected clients.

The data field holds a JSON array of all clients that are connected.

Example:

{align="left"}
	Server -> Client
	{
	  "number": 7,
	  "verb" "CONN",
	  "data": {["alice", "bob"]}
	}

When a client disconnects from the server the server will broadcast a message
containing the ID of the disconnecting client. It is up to clients to remove
the disconnecting client from their list of active clients.

Example:

{align="left"}
	Server -> Client
	{
	  "number": 8,
	  "verb" "DISC",
	  "data": "alice"
	}

## Getting a list of connected clients

If clients send a message containing WHOO the server **SHOULD** send a response
with all currently connected clients. It is then up to the client to update
it's list of currently connected users.

The data field holds a JSON array of all clients that are connected.

{align="left"}
	Server -> Client
	{
	  "number": 9,
	  "verb" "CONN",
	  "data": {["bob", "abc"]}
	}

## Checksums

Each packet being sent should contain a checksum field with the checksum of all
the data in the data field of the packet. The checksum must be in SHA256 format.

Example is shown below.

{align="left"}
	Server -> Client
	{
	  "number": 9,
	  "verb" "CONN",
	  "data": {["bob", "abc"]}
	  "checksum": "0ad320defb0ee98b2b7500589ed330393e9e5791e2a9d1f3e66f519a83fe0830"
	}

## Requesting a packet to be resent

If the server or client does not validate the packet checksum successfully or
didn't recieve a packet it may request that packet to be resent by sending a
RESEND packet where the data field contains the packet number that should be
resent. The original packet will be sent back with no modifications aside from
the checksum being recalculated in case of possible error.

{align="left"}
	Server -> Client
	{
	  "number": 9,
	  "verb" "RESEND",
	  "data": 3
	}

	Client -> Server
	{
	  "number": 7,
	  "verb" "RESEND",
	  "data": 2
	}

{backmatter}
