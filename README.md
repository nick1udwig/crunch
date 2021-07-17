# Crunch

## Why?
Urbit is growing -- but it is unclear how much.

## What?
A set of tools to export Urbit group activity so community growth can be quantified.

## How?
A library to provide the tools.
A generator and thread to make using the library as simple as a command-line call.


# Usage
## Export multiple graphs of data
Make use of the included generator to extract all graph data from your ship (without post content) and output it to the CSV of your choice.
```
|crunch /my-csv-file-name/csv *@da
```

Optional arguments:
* `to`: `@da` up until which post data will be pulled. (TODO: document and debug)
* `groups`: `(list path)` of groups to export; do not export any others. Default: export all.
* `content`: `(unit ?)`, whether to export post content or not. Default: do not export content.

Example usage exporting entire history of a specific group with post content:
```
|crunch /my-csv-file-name/csv *@da, =groups ~[/~zod/fake-zods-test-channel], =content [~ %.y]
```

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

:: output entire history to Dojo with post content in the format:
:: timestamp,ship,group,channel,channel-type,content
(walk-graph.crunch chat-graph %.y ci *@da now)

:: output entire history to csv file at path pax without post content
=pax `path`/chat/csv
|pass (note-write-csv-to-clay.crunch pax (walk-graph.crunch chat-graph %.y ci *@da now))
```

## Export all graph data
The following is example usage of the Crunch library to extract data from all graphs your ship contains and print to the Dojo using the `walk-graph-associations` arm.
In the example, *all graphs the user is a part of will be exported*.
Replace the group and graph with a `%chat` available on your (test) ship.
```
:: import library and types we'll be using
=ms -build-file %/sur/metadata-store/hoon
=crunch -build-file %/lib/crunch/hoon

:: scry metadata about all graphs
=assoc-scry .^(associations:ms %gx /=metadata-store=/app-name/graph/noun)

:: output entire history to Dojo without post content
(~(walk-graph-associations crunch [our now]) assoc-scry %.n *@da now)
```

# ACKnowledgements
Thanks to the Urbit Foundation for funding this project through the [Urbit Apprenticeships program](https://urbit.org/grants/apprenticeships/).
You can find the [bounty here](https://urbit.org/grants/bounties/analytics-script/).

Thanks to `~timluc-miptev`, `~wolref-podlex`, and `~taller-ravnut` for mentorship and giving me this opportunity.
