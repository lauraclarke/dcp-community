### DCP PR:

***Leave this blank until the RFC is approved** then the **Author(s)** must create a link between the assigned RFC number and this pull request in the format:*

`[dcp-community/rfc#](https://github.com/HumanCellAtlas/dcp-community/pull/<PR#>)`

# RFC: Event Journaling and Replay for the Data Store (DSS)

## Summary

This RFC proposes DSS event journaling and replay API endpoints.

## Author(s)

* [Brian Hannafious](mailto:bhannafi@ucsc.edu)

## Shepherd
***Leave this blank.** This role is assigned by DCP PM to guide the **Author(s)** through the RFC process.*

*Recommended format for Shepherds:*

 `[Name](mailto:username@example.com)`

## Motivation

The DSS currently provides an event subscription service. Events are triggered when a bundle is created, tombstoned, or
deleted, and may be filtered by applying [JMESPath](http://jmespath.org/) to the
[metadata](https://github.com/HumanCellAtlas/metadata-schema) documents contained in the bundle. The DSS should also
provide JMESPath filterable, chronological, event replay.

### User Stories

* As a new DSS subscriber, I would like to replay the events prior to my subscription activation date.
* As an existing DSS subscriber, I would like to recover from protracted downtime of my service.
* As a DSS subscriber, I would like to test changes to my JMESPath subscriptions against specific bundles.

## Detailed Design

The DSS will provide new API endpoints:
  1. GET /events, accepting `replica`, and `start-date`. This will return a paged listing of signed urls of s3 objects
     containing event journals.
  1. GET /event, accepting `replica`, `start-date`, `bundle-uuid`, and `bundle-version`. This will return the JSON
     document produced during the bundle event.

The event data for a single event will be the JSON metadata document currently produced for the DSS
JMESPath event subscription service.

Due to the (currently unbounded) size of the JSON metadata document, new events will be stored directly on s3 as single
objects. An offline daemon will compile events into journals as needed.

### Unresolved Questions
