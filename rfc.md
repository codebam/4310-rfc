%%%
title = "FooChat"
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
# initials="S. B., K. D. S."
surname="Behan, Dsane-Selby"
#fullname="Sean Behan, Kevin Dsane-Selby"
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

## Connecting to the server

Clients **MUST** connect to the server using TCP packets. Clients **MUST** also
connect on port **682651**.

To initiate a connection clients **SHALL** send a TCP packet containing the
message LOGN, a username, and a code version number.

The server **SHALL** acknowledge the login by sending a TCP packet containing
SUCC, the user id.

The connection is made as follows:

{align="left"}
	Client -> Server LOGN bob FC1
	Server -> Client SUCC 1

## Disconnecting from the server

Clients **SHOULD** send a GDBY (goodbye) message when disconnecting from the
server.

{align="left"}
	Client -> Server GDBY

## Sending messages

Clients send messages by sending TCP packets with the destination user ID as
well as the message that you want to send.

Example:

{align="left"}
	Client -> Server SEND 2:hello world

If messages are sent to the server ID of 0, that message will be broadcast to
all other clients connected to the server.

{align="left"}
	Client -> Server SEND 0:message to all clients
	Client -> Server SEND all:message to all clients

## Receiving messages

Clients **MUST** implement a receive method such that they are able to identify
the ID of the messages being received. Server messages always come from ID 0.
They will include the user that is sending the message, followed by the content
of the message.

Example:

{align="left"}
	Server -> Client RECV 2:hello world

## Server broadcasts

When a new client connects to the server, the server will broadcast a message
to all clients telling them both the user ID and the username of the new
client. Clients are required to keep track of all connected clients.

Example:

{align="left"}
	Server -> Client CONN 2:bob
	Server -> Client CONN 3:alice

When a client disconnects from the server the server will broadcast a message
containing the ID of the disconnecting client. It is up to clients to remove
the disconnecting client from their list of active clients.

Example:

{align="left"}
	Client -> Server DISC 2

## Getting a list of connected clients

If clients send a message containing WHOO the server **MUST** send a response
with all currently connected clients. It is then up to the client to update
it's list of currently connected users.

{align="left"}
	Client -> Server WHOO
	Server -> Client CONN 1:sara
	Server -> Client CONN 2:bob
	Server -> Client CONN 3:alice

# Error messages

## Sending a message to a non-existing client

If clients try to send messages to clients that aren't connected the server
sends a EROR 1 message indicating that the client is no longer connected. It is
then up to the client to check the list of connected clients using a WHOO
command.

{align="left"}
	Client -> Server SEND 2:hello
	Server -> Client EROR 1
	Client -> Server WHOO
	Server -> Client CONN 1:sara
	Server -> Client CONN 3:alice

{backmatter}
