# BAPCtools

[![pipeline status](https://gitlab.com/ragnar.grootkoerkamp/BAPCtools/badges/master/pipeline.svg)](https://gitlab.com/ragnar.grootkoerkamp/BAPCtools/commits/master)

BAPCtools is a tool for creating and developing problems following the
CLICS (DOMjudge/Kattis) problem format specified [here](https://clics.ecs.baylor.edu/index.php?title=Problem_format).

The aim of this tool is to run all necessary compilation, validation, and
testing commands while working on an ICPC-style problem.
Ideally one should never have to manually run any compilation or testing command themselves.

I'm interested to know who's using this, so feel free to inform me (e.g. via an issue) if so ;)
The current state is relatively stable, but things do change from time to
time since I'm not aware of usage outside of BAPC yet.

## Installation

For now the only way to use this is to clone the repository and install the
required dependencies manually.
(If you know how to make Debian and/or Arch packages, feel free to help out.)

-   Python 3 with the [yaml library](https://pyyaml.org/wiki/PyYAMLDocumentation) via `pip install
    pyyaml` or the `python[3]-yaml` Arch Linux package.
-   The `argcomplete` library for command line argument completion. Install via
    `python[3]-argcomplete`.
	- Note that actually using `argcomplete` is optional, but recommended.
	  Detailed instructions are [here](https://argcomplete.readthedocs.io/en/latest/).
	 
      TL;DR: Put `eval "$(register-python-argcomplete[3] tools.py)"` in your `.bashrc` or `.zshrc`.
-   The `pdflatex` command, provided by `texlive-bin` on Arch Linux and
    potentially some specific LaTeX packages (like tikz) provided by
	`texlive-extra`.
	These are only needed for building `pdf` files, not for `run` and `validate` and such.

After cloning the repository, symlink [bin/tools.py](bin/tools.py) to somewhere in your `$PATH`. E.g., if `~/bin/` is in your `$PATH`, you can do:

```
% ln -s ~/git/BAPCtools/bin/tools.py ~/bin/bt
```

### Windows

For Windows, you'll need the following in your
`path`:
- `Python` for Python 3
- `g++` to compile C++
- `javac` and `java` to compile and run `java`.

Note that colorized output does not work.
Resource limits (memory limit/hard cpu time limit) are also not supported.

## Usage

BAPCtools can be run either from a problem directory or a contest directory. This
is automatically detected by searching for the `problem.yaml` file.

The most common commands and options to use on an existing repository are:

- [`bt run [-v] [submissions [submissions ...]] [testcases [testcases ...]]`](#run)
- [`bt test <submission> [--samples | [testcases [testcases ...]]]`](#test)
- [`bt generate [-v] [--jobs JOBS]`](#generate)
- [`bt validate [-v] [--remove | --move-to DIR] [testcases [testcases ...]]`](#validate)
- [`bt pdf [-v]`](#pdf)

The list of all available commands and options is at [doc/commands.md#synopsis](doc/commands.md#synopsis),
and more information regarding the implementation is at [doc/implementation_notes.md](doc/implementation_notes.md).

### Run

* `bt run [-v] [submissions [submissions ...]] [testcases [testcases ...]]`

Without arguments, the `run` command runs all submissions against all testcases.
Specify one or more submissions and one or more testcases to only run the given submissions against the given testcases.
okjjkjjk

Before running the given submissions, this command first makes sure that all generated testcases are up to date (in case `generators/generators.yaml` was found).

![run](doc/images/run.gif)

By default, `bt run` only prints one summary line per submission, and one additional line for each testcase with an unexpected result. Use `-v` to print one line per testcase instead.

![run -v](doc/images/run-v.gif)


### Test

- `bt test <submission> [--samples | [testcases [testcases ...]]]`

Use the `test` command to run a single submission on some testcases. The submission `stdout` and `stderr` are printed to the terminal instead of verified as an answer file.

![test](doc/images/test.png)

### Generate

- `bt generate [-v] [--jobs JOBS]`

Use the `generate` command to generate the testcases specified in `generators/generators.yaml`. See [doc/generators.md](doc/generators.md) for documentation on this.
Use `-j 0` to disable running multiple jobs in parallel (the default is `4`).

![generate](./doc/images/generate.gif)

### Validate

- `bt validate [-v] [--remove | --move-to DIR] [testcases [testcases ...]]`

Validate all the `.in` and `.ans` for all (given) testcases. It runs all validators from `input_validators` and `output_validators`.

Validators can be one of
 - a single-file program.
 - a multi-file program with all files in a common directory.
 - a .ctd CheckTestData file (this needs the `checktestdata` executable in your `$PATH`).
- a .viva file.

You can use `--remove` to delete all failing testcases or `--move <dir>` to move
them to a separate directory.

![validator](./doc/images/validate.png)

### Pdf

- `bt pdf [-v]`

Use this command to compile the `problem.pdf` from the `problem_statement/problem.en.tex` LaTeX statement.
`problem.pdf` is written to the problem directory itself.

This can also be used to create the contest pdf by running it from the contest directory.
