# Crunch

## Why?
Urbit is growing -- but it is unclear how much.

## What?
A set of tools to export Urbit group activity so community growth can be quantified.

## How?
A library to provide the tools.
A generator and thread to make using the library as simple as a command-line call.


# Usage
*WIP*

The following is example usage of the Crunch library to extract data from a `%chat` graph and print to the Dojo using the `walk-chat-graph` arm.
In the example, the group is `~zod/fake-zods-test-channel` and the graph is `~zod/lounge-5677`.
Replace the group and graph with a `%chat` available on your (test) ship.
```
:: import library and types we'll be using
=res -build-file %/sur/resource/hoon
=crunch-sur -build-file %/sur/crunch/hoon
=crunch -build-file %/lib/crunch/hoon

:: scry the graph and extract it from the scry response
=lounge-scry .^(update:gs %gx /=graph-store=/graph/(scot %p ~zod)/lounge-5677/noun)
=lounge-graph ?.  ?=(%add-graph -.q.lounge-scry)  ~  ?~  graph.q.lounge-scry  ~  graph.q.lounge-scry

:: scry the %chat graph you wish to extract from
=chat-scry .^(update:gs %gx /=graph-store=/graph/(scot %p ~zod)/lounge-5677/noun)

:: build channel-info argument used by walk-chat-graph
=ci `channel-info:crunch-sur`[`resource:res`[entity=~zod name=%fake-zods-test-channel] `resource:res`[entity=~zod name=%lounge-5677] channel-type=%chat]

:: output without and with post content in the format:
:: timestamp,ship,group,channel,channel-type,content
(walk-chat-graph.crunch lounge-graph %.n ci)
(walk-chat-graph.crunch lounge-graph %.y ci)
```


# ACKnowledgements
Thanks to the Urbit Foundation for funding this project through the [Urbit Apprenticeships program](https://urbit.org/grants/apprenticeships/).
You can find the [bounty here](https://urbit.org/grants/bounties/analytics-script/).

Thanks to `~timluc-miptev`, `~wolref-podlex`, and `~taller-ravnut` for this opportunity.
