# procfs

This procfs package provides functions to retrieve system, kernel and process
metrics from the pseudo-filesystems /proc and /sys.

*WARNING*: This package is a work in progress. Its API may still break in
backwards-incompatible ways without warnings. Use it at your own risk.

[![GoDoc](https://godoc.org/github.com/JammyT/procfs?status.png)](https://godoc.org/github.com/JammyT/procfs)
[![Build Status](https://travis-ci.org/prometheus/procfs.svg?branch=master)](https://travis-ci.org/prometheus/procfs)
[![Go Report Card](https://goreportcard.com/badge/github.com/JammyT/procfs)](https://goreportcard.com/report/github.com/JammyT/procfs)

## Usage

The procfs library is organized by packages based on whether the gathered data is coming from
/proc, /sys, or both.  Each package contains an `FS` type which represents the path to either /proc, /sys, or both.  For example, current cpu statistics are gathered from
`/proc/stat` and are available via the root procfs package.  First, the proc filesystem mount
point is initialized, and then the stat information is read.

```go
fs, err := procfs.NewFS("/proc")
stats, err := fs.Stat()
```

Some sub-packages such as `blockdevice`, require access to both the proc and sys filesystems.

```go
    fs, err := blockdevice.NewFS("/proc", "/sys")
    stats, err := fs.ProcDiskstats()
```

## Building and Testing

The procfs library is normally built as part of another application.  However, when making
changes to the library, the `make test` command can be used to run the API test suite.

### Updating Test Fixtures

The procfs library includes a set of test fixtures which include many example files from
the `/proc` and `/sys` filesystems.  These fixtures are included as a [ttar](https://github.com/ideaship/ttar) file
which is extracted automatically during testing.  To add/update the test fixtures, first
ensure the `fixtures` directory is up to date by removing the existing directory and then
extracting the ttar file using `make fixtures/.unpacked` or just `make test`.

```bash
rm -rf fixtures
make test
```

Next, make the required changes to the extracted files in the `fixtures` directory.  When
the changes are complete, run `make update_fixtures` to create a new `fixtures.ttar` file
based on the updated `fixtures` directory.  And finally, verify the changes using
`git diff fixtures.ttar`.
