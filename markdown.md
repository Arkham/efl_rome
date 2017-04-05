layout: true

.footnote[
  .red[@arkh4m] / .italic[EFL Rome] / Erlang Solutions
]

---

class: inverse

# A Little Replica <br> of the Internet <br> in .red[Elixir]

---

# Ciao Roma!

My name is Ju and online I'm .bold[@arkh4m] 🙇

I'm Italian and I live in London 🇬🇧

I work at Erlang Solutions 🥇

---

class: inverse

# What is this talk about?

---

<iframe width="560" height="315" src="https://www.youtube.com/embed/iDbyYGrswtg"
frameborder="0" allowfullscreen></iframe>

---

# Ok.

---

class: inverse
background-image: url(images/pub.jpg)

---

class: inverse

# I. What was the first name of the Internet?

---

# Arpanet

---

class: inverse

# II. When was its first prototype built?

---

# April 1969 - October 1969

---

class: inverse
background-image: linear-gradient(rgba(0, 0, 0, 0.3), rgba(0, 0, 0, 0.3)), url(images/bryan.jpg)

# Summer of '69

---

class: inverse

# III. What was the first message that was trasmitted?

---

# login

---

# .striked[login]

---

class: left

.quote[The message text was the word login; on an earlier attempt the l and the o
letters were transmitted, but the system then crashed. Hence, the literal first
message over the ARPANET was .bold[lo]. About an hour later, after the programmers
repaired the code that caused the crash, the SDS Sigma 7 computer effected a
full login.]

---

# lo

---

class: inverse

# IV. Was ARPANET built to survive a nuclear war?

---

## Nope, that was one of first cases on fake news on the Internet.

---

class: inverse

# V. What were the design goals?

---

## To build a interconnected, distributed and robust network of IMPs, i.e. Interface Message Processors

---

# .bold[Interconnected]

---

# .bold[Distributed]

---

# .bold[Robust]

---

# Ringing any bells? 🔔

---

class: inverse

# .italic["How hard would it be to rewrite the Internet in Elixir?"]

---

# Back to Basics

---

class: inverse
background-image: url(images/router.jpg)
background-size: cover

---

# Routing

The process of selecting a path for traffic in a network

---

# Router

The thing™ which does the routing

---

class: inverse

# How does a router work?

It forwards a packet from one network interface to another network interface. In
order to do so it uses a .bold[routing table], which maps various destinations
to specific interfaces. This routing table has to be updated in real-time.

---

background-image: url(images/map.png)

---

class: inverse

# How can we build a topology of the network?

---

class: left

# Link-state routing protocol

Each router:

1. Determines its direct neighbours
2. Periodically sends a link-state message
3. Creates a map of the network

---

class: left

# Link-state message, you say?

The link-state message contains:

1. The name of the sender
2. The list of directly connected neighbours
3. A number which identifies the version of the message

---

background-image: url(images/map.png)

---

class: inverse

# The routing table

Each node runs an algorithm over the map to determine the shortest path from
itself to every other node in the network. Dijkstra's algorithm is a good choice.

---

class: inverse
background-image: url(images/cats.jpg)
background-size: cover

# Show us the codez!

---

# Thank you! Questions?

.spaced[
  .red[@arkh4m]
]

.spaced[
  erlang-solutions.com
]

.spaced[
  github.com/Arkham/mini_router
]
