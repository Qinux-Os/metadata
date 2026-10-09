# Releases

One JSON file per release, named `YYYY.N.json`, each validating against
[`../schema/release.schema.json`](../schema/release.schema.json).

## Adding a release

Releases are cut by the automation in the
[`release`](https://github.com/Qinux-Os/release) repository, which writes the
file here. Do not add release files by hand except to correct a mistake.

## Why one file per release

So that the current state is a git checkout away from any past state. A single
appended `releases.json` grows without bound and makes every change a rewrite
of a large file.

## The index

`releases/index.json` lists every release, newest first. It is generated from
the files in this directory. Do not edit it by hand.

## Corrections

A bug fix after release does **not** create a new version. The existing file
gains a higher build date in `packages`, and `released` keeps the original
publication time.

This matters because users' package managers have recorded `2026.1`. Creating
`2026.1.1` would leave them on a release that is no longer maintained.
