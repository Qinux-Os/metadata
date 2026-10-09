# Release metadata

Machine-readable facts about Qinux releases. Build tooling and third-party
systems read these files to answer: what versions exist, which is current, what
changed, and is it trustworthy.

## Layout

| Path | Contents |
| --- | --- |
| [`schema/`](schema/) | JSON Schema that every file here is validated against |
| [`channels/`](channels/) | Channel definitions: stable, rolling, testing |
| [`releases/`](releases/) | One file per release |

## Channels

A channel is a named stream of releases with a different update policy.

| Channel | Moves | For |
| --- | --- | --- |
| `stable` | At release time only | Almost all users |
| `rolling` | Continuously | Developers, early adopters |
| `testing` | Continuously, unstable | Contributors testing changes |

`stable` is the default. Users on `stable` receive no change until a release is
published. This is the main reason Qinux is not yet a rolling distribution, and
changing it is a decision recorded in an
[RFC](https://github.com/Qinux-Os/rfcs).

## Reading the metadata

```sh
# the current stable release
curl -s https://metadata.qinux-os.org/releases/index.json | jq .current.stable

# every release
curl -s https://metadata.qinux-os.org/releases/index.json | jq '.releases[].version'
```

Until the first release is cut, `current.stable` is `null` and `releases` is
empty. That is the correct state, not a bug.

## The shape of a release

[`releases/EXAMPLE.json`](releases/EXAMPLE.json) is a filled-in example with
obviously invalid checksums. The real file is written by the release
automation.

```json
{
  "version": "2026.1",
  "channel": "stable",
  "released": "2026-03-14T12:00:00Z",
  "kernel": "6.12.1-qinux",
  "packages": {
    "qinux-base": "20260314"
  },
  "previous": null
}
```

Field meanings are defined in
[`schema/release.schema.json`](schema/release.schema.json). Do not add fields
without updating the schema in the same pull request: a file that validates
against an older schema is worse than no file.

## Version scheme

Calendar versioning: `YYYY.N`, where `N` increments within the year. A
correction reuses the same version with a higher build date; it is never
`2026.1.1`.

Calendar versioning was chosen because Qinux is modular: components are released
independently, so a single monotonic counter would carry no information about
how far along a given system is.

## Validation

Every change is validated in CI:

```sh
qinux-cli metadata validate
```

The schema is authoritative. If a file does not validate, the build fails.

## Relationship to other repositories

| Repository | Holds |
| --- | --- |
| [`repo`](../repo/) | The repository this metadata describes |
| [`mirrors`](../mirrors/) | Where that repository is replicated |
| [`release`](../release/) | The automation that produces both |
| [`package-index`](../package-index/) | Which packages a release contains |

## License

CC0-1.0. There is nothing creative about a release date to protect, and
downstream tools should be able to consume this without attribution
obligations.
