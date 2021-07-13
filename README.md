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

## Export graph data
The following is example usage of the Crunch library to extract data from a `%chat` graph and print to the Dojo or to CSV using the `walk-graph` arm.
In the example, the group is `~zod/fake-zods-test-channel` and the graph is `~zod/lounge-5677`.
Replace the group and graph with a `%chat` available on your (test) ship.
```
:: import library and types we'll be using
=res -build-file %/sur/resource/hoon
=gs -build-file %/sur/graph-store/hoon
=crunch-sur -build-file %/sur/crunch/hoon
=crunch -build-file %/lib/crunch/hoon

:: scry the %chat graph and extract it from the scry response
=chat-scry .^(update:gs %gx /=graph-store=/graph/(scot %p ~zod)/lounge-5677/noun)
=chat-graph ?.  ?=(%add-graph -.q.chat-scry)  ~  ?~  graph.q.chat-scry  ~  graph.q.chat-scry

:: build channel-info argument used by walk-graph
=ci `channel-info:crunch-sur`[`resource:res`[entity=~zod name=%fake-zods-test-channel] `resource:res`[entity=~zod name=%lounge-5677] channel-type=%chat]

:: output to Dojo with post content in the format:
:: timestamp,ship,group,channel,channel-type,content
(walk-graph.crunch chat-graph %.y ci)

:: output to csv file at path pax without post content
=pax `path`/chat/csv
|pass (note-write-csv-to-clay.crunch pax (walk-graph.crunch chat-graph %.y ci))
```

## Export all graph data in a group
The following is example usage of the Crunch library to extract data from all graphs in a group and print to the Dojo using the `walk-graph-associations` arm.
In the example, *all graphs the user is a part of will be exported*.
Replace the group and graph with a `%chat` available on your (test) ship.
```
:: import library and types we'll be using
=ms -build-file %/sur/metadata-store/hoon
=crunch -build-file %/lib/crunch/hoon

:: scry metadata about all graphs
=assoc-scry .^(associations:ms %gx /=metadata-store=/app-name/graph/noun)

:: output to Dojo without post content.
:: a fresh bowl is required for the underlying scry to work,
::  hence the rather long argument to the walk-graph-associations arm
(~(walk-graph-associations crunch `bowl:gall`[[our our %dojo] [*boat:gall *bitt:gall] 0 eny now our %home da+now]) assoc-scry %.n)
```

# ACKnowledgements
Thanks to the Urbit Foundation for funding this project through the [Urbit Apprenticeships program](https://urbit.org/grants/apprenticeships/).
You can find the [bounty here](https://urbit.org/grants/bounties/analytics-script/).

Thanks to `~timluc-miptev`, `~wolref-podlex`, and `~taller-ravnut` for mentorship and giving me this opportunity.
