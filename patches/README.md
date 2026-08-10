# Patches for python-fastmcpport

## Overview

fastmcp is pure Python. No source patches required.

## Rust dependencies

- `pydantic-core`: handled by python-pydantic-coreport
- `rpds-py`: handled by python-rpds-pyport
- `cryptography`: NOT available — mcp pinned to 1.9.4 to avoid this dependency

## mcp version pin

`mcp==1.9.4` is pinned because mcp>=1.10 requires `pyjwt[crypto]` which depends
on the `cryptography` package (Rust/OpenSSL). Once cryptography is ported, the pin
can be removed and fastmcp 3.x becomes installable.

## Test results

- 5 tests per interpreter: tool count/names, add(21,21)=42, greet, pydantic model distance
