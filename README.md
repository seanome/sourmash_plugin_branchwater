# pyo3_branchwater

[![PyPI](https://img.shields.io/pypi/v/pyo3-branchwater)](https://pypi.org/project/pyo3-branchwater/)

tl;dr Do fast and low-memory search/gather of many sourmash sketches
via a sourmash plugin.

## Details

This repo contains a PyO3-based Python wrapper around the
[core branchwater code](https://github.com/sourmash-bio/sra_search).
[Branchwater](https://www.biorxiv.org/content/10.1101/2022.11.02.514947v1)
is a fast, low-memory and multithreaded application for searching very large
collections of
[FracMinHash sketches](https://www.biorxiv.org/content/10.1101/2022.01.11.475838v2)
as generated by [sourmash](sourmash.readthedocs.io/).

For details, see the Rust code in `src/` and Python wrapper in `python/`.

Uses [pyo3](https://github.com/PyO3/pyo3) for the Python-to-Rust wrapping.

This functionality can be used from within sourmash as a command-line
plugin; see below quickstart.

## Documentation

There is a quickstart below, as well as [more documentation here](doc/README.md).

## Quickstart for `manysearch`.

To try out, you'll need to install a branch of sourmash that contains
[sourmash#2438](https://github.com/sourmash-bio/sourmash/pull/2438).

This quickstart demonstrates `manysearch` using
[the 64 genomes from Awad et al., 2017](https://osf.io/vk4fa/).

### First, install this code.

Install this repo in developer mode:
```
pip install -e .
```

### Second, download sketches.

The following commands will download sourmash sketches for them and
unpack them into the directory `podar-ref/`:

```
mkdir -p podar-ref
curl -JLO https://osf.io/4t6cq/download
unzip -u podar-reference-genomes-updated-sigs-2017.06.10.zip
```

### Third, create lists of query and subject files.

`manysearch` takes in lists of signatures to search, so we need to
create those files:

```
ls -1 podar-ref/{2,47,63}.* > query-list.txt
ls -1 podar-ref/* > podar-ref-list.txt
```

### Fourth: Execute!

Now run `manysearch`:
```
sourmash scripts manysearch query-list.txt podar-ref-list.txt -o results.csv
```

You will (hopefully ;) see a set of results in `results.csv`.

## Debugging help

If your file lists are not working properly, try running:
```
sourmash sig summarize query-list.txt
sourmash sig summarize podar-ref-list.txt
```
to make sure everything can be loaded.

## Future thoughts

The speed and functions of this code will probably be brought into
sourmash core in the future, most likely as part of
[sourmash#2230](https://github.com/sourmash-bio/sourmash/pull/2230).
However, in the meantime, this is a fun side project that makes use
of sourmash plugins and Rust to provide some fast functionality
that may be of use to some people, and it can serve as a testbed for
future sourmash functionality.

## Develoer notes

```
make test
```
will run the Python tests.

### Generating a release

Bump version number in `Cargo.toml` and push.

Make a new release on github.

Then pull, and:

```
rm -fr target/wheels/
maturin sdist
```

followed by `twine upload target/wheels/pyo3_branchwater-*.tar.gz`

### Building wheels

You can build a wheel for your current platform with:
```
maturin build
```

---

CTB Aug 2023
