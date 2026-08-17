# fastmcpport

z/OS port of [FastMCP](https://github.com/jlowin/fastmcp) — Python framework for building
[Model Context Protocol (MCP)](https://modelcontextprotocol.io/) servers.

## Status

[![Build Status](https://img.shields.io/badge/z%2FOS-passing-brightgreen)](https://github.com/zopencommunity/fastmcpport)

Built and tested against Python 3.12, 3.13 and 3.14.

## Installation

```sh
zopen install fastmcp
```

The compiled dependencies — `pydantic-core`, `rpds-py`, `cryptography`,
`watchfiles` and `pyyaml` — are runtime dependencies of this port and are
installed for you. There is no separate prerequisite step.

To install from the wheel index instead:

```sh
pip install fastmcp --extra-index-url https://repo.zopen.community/pypi/wheels/simple/
```

## Quick Start

```python
import fastmcp

mcp = fastmcp.FastMCP("My z/OS Server")

@mcp.tool
def hello(name: str) -> str:
    """Say hello from z/OS"""
    return f"Hello {name} from z/OS!"

if __name__ == "__main__":
    mcp.run()   # stdio transport (for MCP clients)
```

`@mcp.tool` and `@mcp.tool()` both work.

## What this port builds

From 3.0, `fastmcp` is a metapackage. The code lives in `fastmcp-slim`, and the
wheel built here is deliberately empty — upstream's `pyproject.toml` sets
`only-include = []`, so their wheel and ours carry a dist-info and nothing else.
What it does carry is a dependency on `fastmcp-slim[client,server]`, and
installing that is what puts the `fastmcp` module on the path.

`fastmcp-slim` is pure Python and needs no port of its own; it comes from PyPI.
So nothing here is compiled and nothing is z/OS-specific in fastmcp itself. What
this port establishes is that the ~65-package closure underneath resolves and
works on z/OS.

Its dependency is not restated in the buildenv. It is read back out of the built
wheel's own `Requires-Dist` and asserted, so a repackaging upstream fails the
build rather than quietly testing a different tree.

## Compiled dependencies

Each has its own port, and each is checked at build time to be sitting at a
version the zopen index publishes:

| Package         | Language | Port                |
| --------------- | -------- | ------------------- |
| `pydantic-core` | Rust     | `pydantic-coreport` |
| `rpds-py`       | Rust     | `rpds-pyport`       |
| `cryptography`  | Rust     | `cryptographyport`  |
| `watchfiles`    | Rust     | `watchfilesport`    |
| `PyYAML`        | C        | `pyyamlport`        |

### cffi

One dependency has no port and cannot get one yet. `cryptography` imports
`_cffi_backend` at import time even though its own bindings are Rust, so cffi is
a hard runtime requirement.

cffi cannot be built on z/OS: it needs libffi, and upstream libffi has no z/OS
backend — its `configure.host` knows `s390-*-*` only, which is Linux on Z and the
wrong ABI. A z/OS libffi means writing XPLINK call and closure trampolines in
assembly.

IBM's Open Enterprise Python ships a working cffi with every interpreter this
port targets (1.17.1 on 3.12, 2.0.0 on 3.13 and 3.14), all of which satisfy
`cffi>=1.12`, so the build takes it from the interpreter via
`ZOPEN_PYTHON_VENV_SYSTEM_SITE`. `cryptographyport` does the same and documents
it at length.

The consequence both ports inherit: **a clean virtual environment created without
`--system-site-packages` cannot install either of them.** That holds until libffi
has a z/OS backend. Use the interpreter directly, or create the venv with
`--system-site-packages`.
