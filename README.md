# Crunch

The Urbit network is growing -- but it is unclear how much.
Crunch is a set of tools to export Urbit group activity so community growth can be quantified.
It consists of a library to provide the tools, and a generator to make using the library as simple as a command-line call.


# Usage
## Export graphs using `|crunch` generator
Use the `|crunch` generator to export all group graph data from your ship (without post content) and output it to a CSV file at the specified path:
```
|crunch /path/to/my-csv-file/csv *@da
```

The `|crunch` generator accepts the following arguments:

Argument  | Type          | Optional? | Description
--------- | ------------- | --------- | -----------
`path`    | `path`        | No        | Where the CSV data should be exported. Must end with a `csv` mark.
`from`    | `@da`         | No        | Date after which data will be exported (oldest to include).
`to`      | `@da`         | Yes       | Date until which data will be exported (newest to include). **Default**: `now`.
`groups`  | `(list path)` | Yes       | Groups to export; do not export any others. **Default**: export all.
`content` | `(unit ?)`    | Yes       | Whether to export post content or not. **Default**: do not export content.

To demonstrate using the arguments, the following exports activity of two specific groups, between 10 days ago and 5 days ago, with post content:
```
|crunch /group-dumps/ten-to-five/csv (sub now ~d10), =to (sub now ~d5), =groups ~[/~zod/fake-zods-test-channel /~bus/fake-buss-test-channel], =content [~ %.y]
```

## Advanced usage: `crunch` library
### Export specific `%chat` graph data
The following is example usage of the `crunch` library to export data from a `%chat` graph and print to the Dojo or to CSV using the `walk-chat-graph` arm.
In the example, the group is `~zod/fake-zods-test-channel` and the graph is `~zod/lounge-5677`.
Replace the group and graph with the desired `%chat` graph.
```
:: import library and types we'll be using
=gs -build-file %/sur/graph-store/hoon
=crunch-sur -build-file %/sur/crunch/hoon
=crunch -build-file %/lib/crunch/hoon

:: scry the `%chat` graph and extract it from the scry response
=chat-scry .^(update:gs %gx /=graph-store=/graph/(scot %p ~zod)/lounge-5677/noun)
=chat-graph ?.  ?=(%add-graph -.q.chat-scry)  ~  ?~  graph.q.chat-scry  ~  graph.q.chat-scry

:: build `channel-info` argument used by `walk-chat-graph`
=ci `channel-info:crunch-sur`[[entity=~zod name=%fake-zods-test-channel] [entity=~zod name=%lounge-5677] channel-type=%chat]

:: output entire history to Dojo with post content in the format:
:: timestamp,ship,group,channel,channel-type,content
(walk-chat-graph.crunch chat-graph %.y ci *@da now)

:: output entire history to csv file at path pax without post content
=pax `path`/chat/csv
|pass (note-write-csv-to-clay.crunch pax (walk-chat-graph.crunch chat-graph %.y ci *@da now))
```

### Export specific non-`%chat` graph data
Data can also be exported from `%links` or `%publish` graphs.
The most recent edit for a given post or comment will be retained; all earlier versions will not be exported.

In the example, the group is `~bus/fake-buss-test-channel` and the graph is `~bus/buss-notes-2713` (a `%publish` graph).
Replace the group and graph with the desired `%links` or `%publish` graph.
```
:: import library and types we'll be using
=gs -build-file %/sur/graph-store/hoon
=crunch-sur -build-file %/sur/crunch/hoon
=crunch -build-file %/lib/crunch/hoon

:: scry the `%notes` graph and extract it from the scry response
=notes-scry .^(update:gs %gx /=graph-store=/graph/(scot %p ~bus)/buss-notes-2713/noun)
=notes-graph ?.  ?=(%add-graph -.q.notes-scry)  ~  ?~  graph.q.notes-scry  ~  graph.q.notes-scry

:: build `channel-info` argument used by `walk-nested-graph-for-most-recent-entries`
=ci `channel-info:crunch-sur`[[entity=~bus name=%fake-buss-test-channel] [entity=~bus name=%buss-notes-2713] channel-type=%publish]

:: output last ten days of history to Dojo with post content in the format:
:: timestamp,ship,group,channel,channel-type,content
(walk-nested-graph-for-most-recent-entries.crunch notes-graph %.y ci (sub now ~d10) now)
```


### Export all graph data
The following is example usage of the `crunch` library to export data from all graphs your ship contains and print to the Dojo using the `walk-graph-associations` arm.
In the example, *all graphs the user is a part of will be exported*.
```
:: import library and types we'll be using
=ms -build-file %/sur/metadata-store/hoon
=crunch -build-file %/lib/crunch/hoon

:: scry metadata about all graphs
=assoc-scry .^(associations:ms %gx /=metadata-store=/app-name/graph/noun)

:: output entire history to Dojo without post content
(~(walk-graph-associations crunch [our now]) assoc-scry %.n *@da now)
```


# Performance
Testing on my personal ship on 210724 with pier size `3.4GB`.
Run on an Intel i7-1065G7.
The `|crunch` generator took less than one minute to export the entire history of all graphs without post content.
With post content, took around one-and-a-half minutes.
The resulting CSV was `31MB` without post content or `56MB` with post content.


# Acknowledgements
Thanks to the Urbit Foundation for funding this project through the [Urbit Apprenticeships program](https://urbit.org/grants/apprenticeships/).
You can find the [bounty here](https://urbit.org/grants/bounties/analytics-script/).

Thanks to `~timluc-miptev`, `~wolref-podlex`, and `~taller-ravnut` for mentorship and giving me this opportunity.
Shout out to the folks in `~hiddev-dannut/new-hooniverse` for their knowledge, patience, and assistance.
