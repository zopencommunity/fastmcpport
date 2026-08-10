# python-fastmcpport

z/OS port of [FastMCP](https://github.com/jlowin/fastmcp) — Python framework for building
[Model Context Protocol (MCP)](https://modelcontextprotocol.io/) servers.

## Status

[![Build Status](https://img.shields.io/badge/z%2FOS-passing-brightgreen)](https://github.com/zopencommunity/python-fastmcpport)

## Prerequisites

```sh
zopen install python-pydantic-core python-rpds-py
```

## Installation

```sh
zopen install python-fastmcp
```

## Quick Start

```python
import fastmcp

mcp = fastmcp.FastMCP("My z/OS Server")

@mcp.tool()
def hello(name: str) -> str:
    """Say hello from z/OS"""
    return f"Hello {name} from z/OS!"

if __name__ == "__main__":
    mcp.run()   # stdio transport (for MCP clients)
```

## Version pins

- `fastmcp==2.0.0` (2.x series, direct dep on mcp)
- `mcp==1.9.4` (pinned: mcp>=1.10 requires `pyjwt[crypto]` → `cryptography` which is also Rust)

When `cryptography` is ported to z/OS, the mcp pin can be relaxed to allow fastmcp 3.x.

## Rust dependencies

- `pydantic-core` (via pydantic) — `python-pydantic-coreport`
- `rpds-py` (via referencing→jsonschema→mcp) — `python-rpds-pyport`
