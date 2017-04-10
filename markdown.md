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

# Show us the codez

---

class: inverse

# Map

---

```elixir
defmodule WideWeb.Map do
  defstruct data: %{}

  def new, do: %__MODULE__{}
  def new(data), do: %__MODULE__{data: data}

  def update(%__MODULE__{data: data} = map, node, links) do
    %{map | data: Map.put(data, node, links)}
  end

  def reachable(%__MODULE__{data: data}, node) do
    Map.get(data, node, [])
  end

  def all_nodes(%__MODULE__{data: data}) do
    # get list of all nodes
  end
end
```

---

```elixir
iex(1)> alias WideWeb.Map
WideWeb.Map

iex(2)> map = Map.new
%WideWeb.Map{data: %{}}

iex(3)> map = Map.update(map, :berlin, [:london, :paris])
%WideWeb.Map{data: %{berlin: [:london, :paris]}}

iex(4)> Map.reachable(map, :london)
[]

iex(5)> Map.reachable(map, :berlin)
[:london, :paris]

iex(6)> Map.all_nodes(map)
[:berlin, :london, :paris]
```

---

class: inverse

# Dijkstra implementation

---

```elixir
defmodule WideWeb.Dijkstra do
  def route(routing_table, node) do
    case Map.fetch(routing_table, node) do
      {:ok, gateway} -> {:ok, gateway}
      :error         -> :not_found
    end
  end
end
```

---

```elixir
defmodule WideWeb.Dijkstra do
  def table(gateways, map) do
    sorted_list = map
                  |> WideWeb.Map.all_nodes
                  |> Enum.map(fn(node) ->
                    case node in gateways do
                      true  -> {node, 0, node}
                      false -> {node, :infinity, :unknown}
                    end
                  end)
                  |> List.keysort(1)

    iterate(%{}, sorted_list, map)
  end
end
```

---

```elixir
defmodule WideWeb.Dijkstra do
  def iterate(routing_table, [], _map) do
    routing_table
  end
  def iterate(routing_table, [{_, :infinity, _}|_rest], _map) do
    routing_table
  end
  def iterate(routing_table, [{dest, hops_count, gw}|list], map) do
    new_list = map
               |> WideWeb.Map.reachable(dest)
               |> Enum.reduce(list, fn(node, acc) ->
                 update(acc, node, hops_count+1, gw)
               end)

    iterate(Map.put(routing_table, dest, gw), new_list, map)
  end
end
```

---

```elixir
iex(1)> alias WideWeb.{Dijkstra, Map}
[WideWeb.Dijkstra, WideWeb.Map]

iex(2)> gateways = [:madrid, :paris]
[:madrid, :paris]

iex(3)> map = Map.new(%{madrid: [:berlin], paris: [:rome, :madrid]})
%WideWeb.Map{data: %{madrid: [:berlin], paris: [:rome, :madrid]}}

iex(4)> Dijkstra.table(gateways, map)
%{berlin: :madrid, madrid: :madrid, paris: :paris, rome: :paris}
```

---

class: inverse

# Interface Set

---

```elixir
defmodule WideWeb.InterfaceSet do
  def new

  def add(%__MODULE__{data: data} = set, name, ref, pid)

  def remove(%__MODULE__{data: data} = set, name)

  def lookup(%__MODULE__{data: data}, name)

  def ref(%__MODULE__{data: data}, name)

  def name(%__MODULE__{data: data}, ref)

  def list(%__MODULE__{data: data})

  def broadcast(%__MODULE__{data: data}, message)
end
```

---

class: inverse

# Link-state History

---

```elixir
defmodule WideWeb.History do
  defstruct root: nil, data: %{}

  def new(root) do
    %__MODULE__{root: root}
  end

  def check(%__MODULE__{root: root}, root, _count), do: :old
  def check(%__MODULE__{data: data} = history, node, count) do
    current_count = Map.get(data, node, 0)

    if count > current_count do
      {:new, %{history | data: Map.put(data, node, count)}}
    else
      :old
    end
  end
end
```

---

class: inverse

# Router

---

class: left

# What does a router have

* a name, like :london
* a link-state version counter
* a history of received link-state messages
* a set of interfaces
* a routing table
* a map of the network

---

```elixir
defmodule WideWeb.Router do
  @broadcast_interval 3_000

  defmodule State do
    defstruct name: nil, n: nil, history: nil, interfaces: nil,
      table: nil, map: nil
  end

  def init(name) do
    interfaces = InterfaceSet.new()
    map = Map.new()
    table = Dijkstra.table([name], map)
    history = History.new(name)

    Process.send_after(self(), :broadcast, @broadcast_interval)

    router(%State{name: name, n: 0, history: history,
                  interfaces: interfaces, table: table, map: map})
  end
end
```

---

class: left

# What does the router do

* determine which nodes he's connected to
* announce his direct neighbours in a link-state message
* forward all new link-state messages to his neighbours
* route messages

---

```elixir
  def router(%State{name: name, n: n, history: history,
                    interfaces: interfaces, table: table,
                    map: map} = state) do
    receive do
      # Interface management
      {:add, node, pid} ->

      {:remove, node} ->

      {:DOWN, ref, :process, _, _} ->

      # Incoming link-state messages
      {:links, node, version, links} ->

      # Update routing table
      :update ->

      # Broadcast new version of link-state message
      :broadcast ->

      # Route a message
      {:route, to, from, message, ttl} ->
    end
  end
```

---

class: inverse
background-image: url(images/cats.jpg)
background-size: cover

# DEMO DEMO DEMO

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
