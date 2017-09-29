+++
authors = ["hlinnakangas", "dgustafsson", "ascherbaum"]
title = "Merging new PostgreSQL versions into Greenplum Database"
short = "This blog post explains how we plan to merge newer PostgreSQL versions into Greenplum Database."
draft = true
date = "2017-09-07T20:00:00Z"
categories = ["Greenplum Database","PostgreSQL", "Greenplum"]

+++

[_Greenplum Database_](http://greenplum.org/) (short: GPDB) was originally based on [_PostgreSQL_](https://www.postgresql.org/) 7.4, and up to version 8.2 every new _PostgreSQL_ version was merged. Then the decision was made to fork away from _PostgreSQL_. With version 5.0, we have [merged _PostgreSQL_ 8.3](http://engineering.pivotal.io/post/gpdb_merge_with_postgresql_8.3/) and learned quite a lot along the way. One of the most important lessons is that it is not enough to just merge the database code - the tooling around the database needs to evolve as well, and customers and users want and need an upgrade path. Greenplum Database 5.0 was released in September 2017.

Time to start thinking about the next steps!

## Development

The development of GPDB takes place on [_GitHub_](https://github.com/greenplum-db/gpdb). After cutting the 5.0 branch ([5X_STABLE](https://github.com/greenplum-db/gpdb/tree/5X_STABLE)), the master branch is again open for new features and patches, which will go into the next major release. Parallel to the development of new features, the next _PostgreSQL_ version (8.4) is merged.

In order to not "disturb" the main repository with too many commits from the merge, this process was moved into a separate [public repository](https://github.com/greenplum-db/gpdb-postgres-merge). Every iteration (chunks of patches in upstream) has it's own branch (iteration_1, iteration_2, and so on) in this repository.

Several major features are approached in separate steps and not as part of the merge process. In particular the Windowing functions create a large number of conflicts, as _Greenplum Database_ has it's own implementation, which was already there when this feature was added to _PostgreSQL_.

Another problematic part is the replication code. The existing file-based replication in _Greenplum Database_ will be replaced with [WAL Replication](https://www.postgresql.org/docs/8.4/static/wal-intro.html) (Write-Ahead Logging) from upstream, which was introduced in _PostgreSQL_ 9.0. This feature offers more flexibility for setting up secondary segments, but also requires refactoring of management tools.

Anyone is welcome to clone this repository and look into merge conflicts. After all conflicts in this repository are solved, and all tests pass, it will be merged back into the main repository.


## Testing

The master repository runs a [_Concourse CI_](https://concourse.ci/) pipeline which constantly [tests every new PR and commit](https://gpdb.ci.pivotalci.info/teams/gpdb/pipelines/gpdb_master). This CI is public. Every time the CI goes red, development is stopped shortly and the reason for the failure is investigated.

For the merge, a [separate pipeline](https://gpdb.ci.pivotalci.info/teams/gpdb/pipelines/postgres_merge) is monitoring the merge repository. We expect this pipeline to be more read than green, nevertheless it will show the progress which is made while resolving all the conflicts.


## Communication

We have several means of communication:

* [Mailing Lists](http://greenplum.org/mailing-lists/)
* [#Greenplum](irc://freenode.net/#greenplum) [IRC](https://en.wikipedia.org/wiki/Internet_Relay_Chat) channel on [freenode](https://freenode.net/)
* [Greenplum](https://greenplum.slack.com/) on [Slack](https://slack.com/)


## How can you help?

If you want to follow the merge process, the best place is to checkout the [repository](https://github.com/greenplum-db/gpdb-postgres-merge) and look for git merge conflicts. Also join the #current-merge-work on Slack.
