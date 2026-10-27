# CPR — Compile & Run 

A lightweight Bash utility for competitive programming. **CPR** compiles a C++ source file with GCC and immediately executes the resulting binary, with support for input redirection, configurable compiler settings, debugging, and execution timeouts.

No IDE-specific build system or project configuration required.

## Features

- **One-command execution:** Compile and run a C++ program with a single command.
- **Input redirection:** Supply an input file or enter input interactively.
- **Clean output capture:** Redirect your solution's output to a file without capturing CPR's status messages.
- **Execution timeout:** Enforce a default 2-second limit, configurable per invocation.
- **Debug mode:** Compile with debugging symbols and without optimizations.
- **Configurable compiler:** Customize the compiler, C++ standard, optimization level, warning flags, and default timeout.
- **Minimal setup:** Works directly from the Linux terminal.

## Requirements

- Bash
- GCC (`g++`)
- GNU Coreutils (`timeout`)

On Ubuntu or Linux Mint, install the dependencies with:

```bash
sudo apt update
sudo apt install build-essential coreutils
```

## Installation

Create the directory for your personal scripts:

```bash
mkdir -p ~/.local/bin
```

Create the script:

```bash
nano ~/.local/bin/cpr
```

Paste the CPR script into the file, save it, and exit Nano. Then make it executable:

```bash
chmod +x ~/.local/bin/cpr
```

Ensure `~/.local/bin` is in your `PATH`. If necessary, add the following line to `~/.bashrc`:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Reload the shell configuration:

```bash
source ~/.bashrc
```

Verify the installation:

```bash
cpr
```

Running CPR without arguments displays its usage instructions.

## Usage

```bash
cpr [options] <source.cpp> [input.txt]
```

The input file is optional. If omitted, the program reads from standard input.

### Examples

**Compile and run using an input file**

```bash
cpr filename.cpp input.txt
```

**Compile and run interactively**

```bash
cpr filename.cpp
```

**Compile and run in debug mode**

```bash
cpr -d filename.cpp input.txt
```

**Set a custom execution timeout**

```bash
cpr -t 5 filename.cpp input.txt
```

**Combine debugging mode with a custom timeout**

```bash
cpr -d -t 5 filename.cpp input.txt
```

**Disable the timeout**

```bash
cpr -t 0 filename.cpp input.txt
```

**Save only the solution's standard output to a file**

```bash
cpr filename.cpp input.txt > output.txt
```

This is particularly useful for comparing your output against expected results. CPR's status messages and compiler diagnostics remain visible in the terminal because they are written to standard error.

**Capture standard error separately**

```bash
cpr filename.cpp input.txt > output.txt 2> errors.txt
```

This redirects CPR's messages, compiler diagnostics, and the program's standard error to `errors.txt`, while `output.txt` receives the solution's standard output.

## Options

| Option | Description |
|---|---|
| `-d` | Enable debug compilation using `-O0` and `-g`. |
| `-t seconds` | Set the execution timeout in seconds. |
| No `-t` option | Use the default 2-second timeout, unless overridden in the configuration file. |
| `-t 0` | Disable the execution timeout. |

Timeout values may be fractional, such as `-t 0.5`. Options can be combined, for example, `cpr -d -t 5 filename.cpp input.txt`.

## Default Compiler Settings

CPR uses the following default configuration:

```bash
CXX="g++"

CXXFLAGS=(
    -std=gnu++20
    -O2
    -pipe
    -Wall
    -Wextra
    -Wshadow
)
```

This enables GNU C++20, optimization, and common compiler warnings.

### Custom Configuration

You can override the defaults without editing the main script.

Create the configuration directory and file:

```bash
mkdir -p ~/.config/cpr
nano ~/.config/cpr/config.sh
```

For example:

```bash
CXX="g++"

CXXFLAGS=(
    -std=gnu++23
    -O2
    -pipe
    -Wall
    -Wextra
    -Wshadow
)

TIMEOUT=3
```

Save the file. CPR loads this configuration whenever it runs.

You can customize the compiler, compilation flags, and default timeout. Command-line options such as `-t` can override the configured timeout for an individual run.

## How It Works

1. Loads the default compiler settings and optional user configuration.
2. Parses the command-line options and validates the supplied files.
3. Compiles the source file into an executable in the same directory.
4. Executes the compiled program, optionally redirecting input from a file.
5. Applies the configured execution timeout.
6. Reports the execution status and returns the program's exit status.

The generated executable uses the source file's basename. For example, `A.cpp` produces `A`.

Debug mode adds `-O0` and `-g` to the compiler flags. It is intended for debugging rather than performance benchmarking.

### Output and Error Handling

CPR separates its own messages from the solution's output:

- **Standard output (`stdout`):** Reserved for the solution's output, making it easy to redirect to a file.
- **Standard error (`stderr`):** Used for CPR status messages, usage errors, and compiler diagnostics. The compiled program's own standard error also remains visible here by default.

This means that:

```bash
cpr filename.cpp input.txt > output.txt
```

produces an output file containing the solution's standard output only, without CPR's compilation or execution messages.

## Notes

- The timeout applies to program execution, not compilation.
- In interactive mode, the timeout also counts time spent waiting for input. Use `-t 0` or increase the timeout when needed.
- CPR returns the program's exit status, allowing failures to be detected by shell scripts.
- Compilation errors stop execution; an old executable is not run after a failed compilation.
- CPR is a local development tool. Submit the C++ source file—not the Bash script—to online judges.

---

**Built with ❤️ by Aditya Shankar Nath**