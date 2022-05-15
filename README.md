# Simple Servers in JavaScript

In these exercises, we're going to try to understand more about how computers talk
to each other over a network. To do that, we're going to actually build a
handful of very simple internet servers and connect to them using tools
that come with our computers.

## Contents <!-- omit in toc -->

- [Getting Started](#getting-started)
- [Why We Are Doing This](#why-we-are-doing-this)
- [The Basics of Computer Networking](#the-basics-of-computer-networking)
- [Hosts and Ports](#hosts-and-ports)
  - [Host Names vs. IP Addresses](#host-names-vs-ip-addresses)
  - [Default Ports](#default-ports)
- [Using Netcat — The `nc` Command](#using-netcat--the-nc-command)
- [Server Software vs. Server Hardware](#server-software-vs-server-hardware)
- [Iterations](#iterations)
  - [Time Server](#time-server)
  - [Echo Server](#echo-server)
  - [Hot or Cold Server](#hot-or-cold-server)
  - [MOTD Server](#motd-server)
  - [Document Server](#document-server)
  - [Your Own Server (Stretch)](#your-own-server-stretch)

## Getting Started

1. Fork this repository
1. Clone the repository to your computer using `git clone`
1. Run `npm install`
1. Skim this README before diving into the iterations
1. Look at the [Iterations section](#iterations) to get started

## Why We Are Doing This

The idea of "writing a network server in JavaScript" probably seems crazy, but our
first server will be about 20 lines long (excluding comments). This is a
real-deal server. You could put it on an [Amazon EC2 instance](http://aws.amazon.com/ec2/instance-types/), run it, and any computer
in the world would be able to connect to it.

Server software is no more complicated than any other kind of software. In fact,
we'll be re-designing some of our command-line software to run remotely and
you'll see how little "re-design" is actually needed.

## The Basics of Computer Networking

The most common type of computer networking involves one computer acting as
a "client" and another acting as a "server."  The server is running some
software that the client wants to make use of. It connects to the server, uses
the software (as the server permits), and then either ends the connection itself
or waits for the server to end the connection.

One of the easiest-to-understand examples is a file server. There's a computer
somewhere with a bunch of files on it. Other computers connect to it in order
to access those files. Perhaps the file server allows users to copy files to
their own computers but not delete them from the server itself.

Servers listen for incoming connections while clients make outbound connections.

## Hosts and Ports

To connect to a service running on another computer, you need two pieces of
information: a host name (or IP address) and a port number.

The host name (or IP address) identifies the computer on the internet, much
like a phone number does on a telephone network. The port number is an
extra bit of information which tells the remote computer which service
we want to use, much like a telephone extension.

We need this extra piece of information because a single computer can be running
multiple pieces of server software, e.g., a web server, a file server, a mail
server, and so on. Each server runs on a single port, so that the remote
computer knows which piece of server software is responsible for handling the
client.

Host names look like "cnn.com", "facebook.com", and so on. IP addresses look
like "74.125.239.38", "173.252.110.27", and so on. Port numbers are just
plain numbers. If you've ever seen a URL that looks like

```text
www.google.com:1234
```

That `1234` is a port number.

### Host Names vs. IP Addresses

In the early days of the internet, there were only IP addresses. Imagine having to remember `172.217.6.78` instead of `google.com`!

These days we use a system called DNS (domain name system), which translates hostnames like `google.com` into IP addresses for us. Domain names are read right-to-left, so there's a single entity responsible for managing every `.com` domain.

Run the following commands on the command line to see DNS do its thing:

```console
host google.com
host facebook.com
host davidson.edu
```

When we type in a URL like `google.com`, we first look up its IP address using DNS.

### Default Ports

By convention, some server types are associated with specific port numbers. For example, web servers (by convention) use port `80`. Your browser respects this convention, so when you visit a URL like

```text
http://www.google.com
```

your browser makes a network request to Google.com on port `80` without you having to specify it.

There's always a port involved, however.

## Using Netcat — The `nc` Command

Netcat (the `nc` command) is a general tool for connecting to remote servers and sending/receiving data.

If you're stuck in an `nc` environment you can press `Ctrl+C` or `Ctrl+D` to exit.

## Server Software vs. Server Hardware

Software engineers use the word "server" to mean two different things:

1. The computer on which server software is running
2. The server software itself

To a computer (the client) trying to connect to a remote service, the
distinction doesn't much matter, which is why engineers use the word to mean
both things in casual conversation.

Likewise, when we say "client" we sometimes mean the software making the
connection (e.g., your web browser) or the computer on which that software is
running.

Since we'll be running our server software on our own computers, we'll
almost always mean "server software" when we say "server."  We'll let you know
when the distinction is important.

## Iterations

This project involves writing a handful of small, independent servers. You can
build them in any order, but we recommend building them in the order presented
below.

To connect to a server, use the `nc` command that comes with your computer. `nc` is short for "netcat", a generic tool for connecting to servers and sending data over the network.

**Note**: Other people can connect to your servers, but they need your IP address. On a Mac, run the following command to get your IP address:

```console
ipconfig getifaddr en0
```

### Time Server

We've built `time-server.js` for you to show you what a basic network server
looks like and to walk you through the process of connecting to one.  To get
started, run this command in your terminal:

```text
node time-server.js
```

You should see the text "Waiting for connections on port 2000...", like so:

![time-server.js][time-server-screenshot]

To connect to this time server, open a *new* terminal window and run

```text
nc 127.0.0.1 2000
```

`127.0.0.1` is a special IP address that always means "my own computer" and
`2000` is the port on which our time server is running.  Netcat should exit
almost immediately, but not before receiving the current time from the server,
like so:

![connecting to the time server][time-client-screenshot]

### Echo Server

**Note**: The echo server is incomplete and it's your job to complete it.
Keep reading to see what an "echo server" is supposed to do.

An "echo server" reads a single line of input from any client that connects,
echoes that same line back to the client, closes the connection, and then starts
waiting again for the next client to connect.

In other words, the server works like this:

1. Wait for a client to connect
1. Once the client connects, wait for them to send data
1. Whatever data the client sends, send that same data back to the client (hence "echo")
1. Close the connection
1. Wait for another client

To start the server, run this command:

```console
node echo-server.js
```

You can use `nc` to connect to it on port `2001`, but it won't do anything yet.
You will have to edit `echo-server.js` and implement the intended functinality.
Open up `echo-server.js` and take a stab at
implementing the "echo" functionality.

To check whether your server is doing what you expect, connect to it using
the `nc` command and type a line of text.  You should see something like this
once its working correctly:

![connecting to the echo server][echo-client-screenshot]

Feel free to change its behavior — the point is to understand what's going on
and get feedback on your implementation!

### Hot or Cold Server

Let's hook up the "hot or cold" kata from Week 1 to a server.  If you don't
remember, the "hot or cold" kata is a command-line guessing game that tells
us whether our guess is too high, too low, or just right.

See `hot-or-cold-server.js` for more details.

### MOTD Server

"MOTD" stands for "message of the day."  Starting from `motd-server.js`, write a
server that sends every client the contents of `motd.txt`.

### Document Server

Create a file called `document-server.js` and make it work as follows:

1. Create a directory named `files/` and add some random text files.
1. When a client connects, they enter a filename and hit enter
1. The server looks in the `files/` for the appropriate file and responds with the contents of the file (if it exists).
1. The server closes the connection

For example, if `files/hello.txt` contained

```text
Hello, world! How are you today?
```

Then a client should be able to connect and type `hello.txt` to see the contents of that file.

### Your Own Server (Stretch)

Think up an idea for your own server and implement it!

[time-server-screenshot]:https://user-images.githubusercontent.com/37304/168487635-7e084b9e-024b-4fc0-83e2-e1cc1a47c2f3.png
[time-client-screenshot]:https://user-images.githubusercontent.com/37304/168487691-7f8352c2-b247-4dae-b763-ae6c0cc732f5.png
[echo-server-screenshot]:https://user-images.githubusercontent.com/37304/168487747-07f731ba-6db5-474f-8c9b-9b5c0588b63d.png
[echo-client-screenshot]:https://user-images.githubusercontent.com/37304/168487905-7aa5e6b3-d8f4-408a-b22b-5f8592bb9aff.png
