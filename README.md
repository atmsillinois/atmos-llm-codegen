# atmos-llm-codegen

Claude agents and skills for generating machine learning training scripts for climate and weather forecasting applications.

## Overview

This repository contains Claude Code agents and skills designed to assist researchers in developing ML training pipelines for atmospheric science. The agents help generate, configure, and optimize training scripts for weather and climate forecasting models.

## Features

- **ML Training Script Generation**: Claude agents that generate PyTorch/TensorFlow training scripts tailored for atmospheric data
- **Climate Forecasting Workflows**: Skills for building end-to-end ML pipelines for weather prediction tasks
- **ERA5 Integration**: Works with an external MCP server that provides Python code generation for reading weather observations from the [ERA5 dataset](https://www.ecmwf.int/en/forecasts/dataset/ecmwf-reanalysis-v5)

## Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
- Python 3.10+
- Access to ERA5 data (via the companion MCP server)

## Installation

```bash
git clone https://github.com/atmsillinois/atmos-llm-codegen.git
cd atmos-llm-codegen
```

## Usage

### Skills

This repository includes Claude Code skills that can be invoked during a session:

| Skill | Description |
|-------|-------------|
| `typer-cli` | Creates or extends Python CLIs using Typer and wires them into pyproject.toml entry points |

To use a skill, ask Claude to perform the related task (e.g., "add a new CLI command") or invoke it directly with `/typer-cli`.

## Related Projects

- ERA5 MCP Server (external repository) - Provides code generation capabilities for reading ERA5 weather observation data

## License

BSD 3-Clause License - See [LICENSE](LICENSE) for details.

## Contributing

Contributions are welcome. Please open an issue to discuss proposed changes before submitting a pull request.