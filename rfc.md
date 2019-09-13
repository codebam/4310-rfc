%%%
title = "FooChat"
abbrev = "Protocol for chatting with foolish people"
ipr = "trust200902"
area = "Internet"
workgroup = "Network Working Group"
submissiontype = "IETF"
keyword = [""]
#date = 1997-04-01T00:00:00Z

[seriesInfo]
name = "RFC"
value = "1407"
stream = "IETF"
status = "informational"

[[author]]
# initials="S. B., K. D. S."
surname="Behan, Dsane-Selby"
#fullname="Sean Behan, Kevin Dsane-Selby"
#abbrev = "S & K"
#role = "editor"
#organization = "AT&T Labs Research"
#  [author.address]
#  email = "bellovin@acm.org"
#  phone = "+1 973-360-8656"
#  [author.address.postal]
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

## Terminology

The keywords **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**,
**SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL**, when they appear in this
document, are to be interpreted as described in [@!RFC2119].

# Syntax

# Connecting to the server

Clients **MUST** connect to the server using TCP packets. Clients **MUST** alsuuuuo
connect on port **682651**.

To initiate a connection clients **SHALL** send a TCP packet containing the message LOGN and a username.

The server **SHALL** acknowledge the login by sending a TCP packet containing SUCC and the user id.

The connection is made as follows:

{align="left"}
	Client -> Server LOGN bob
	Server -> Client SUCC 1

{backmatter}
