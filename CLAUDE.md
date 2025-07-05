# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is the ERC-7730 (Clear Signing Metadata) Registry - a community-maintained registry of metadata files that enable clear signing for EVM blockchain transactions and messages. The registry provides standardized metadata for contracts and EIP-712 messages to improve transaction transparency and security.

## Essential Commands

**Validation and Linting:**
```bash
# Validate ERC-7730 descriptor files (requires Python 3.12+ and erc7730 package)
pip install erc7730
erc7730 lint registry/**/eip712-*.json registry/**/calldata-*.json

# Format descriptors (auto-formats all ERC-7730 files)
erc7730 format

# Validate a specific file
erc7730 lint registry/entity/calldata-Contract.json

# Validate with verbose output for debugging
erc7730 lint --verbose registry/**/eip712-*.json registry/**/calldata-*.json

# Validate with GitHub Actions integration (used in CI)
erc7730 lint registry/**/eip712-*.json registry/**/calldata-*.json --gha
```

**Generate New Descriptors:**
```bash
# Generate descriptor from Etherscan (requires ETHERSCAN_API_KEY in .env)
pipenv run erc7730 generate --address 0xContractAddress --chain-id 1 --owner "Entity Name" --url "https://entity.url"

# Generate from local ABI file
pipenv run erc7730 generate --address 0xContractAddress --chain-id 1 --abi path/to/abi.json
```

**Installation Requirements:**
- Python 3.12+
- `erc7730` package from PyPI
- `pipenv` for virtual environment management
- All validation happens through the erc7730 CLI tool

## Architecture

**Core Structure:**
- `registry/` - Main registry containing entity-specific metadata organized by project/protocol
- `specs/` - ERC-7730 specification and JSON schema files
- `ercs/` - Standard ERC token metadata (ERC20, ERC721, ERC4626, etc.)

**Registry Organization:**
- Each entity has a dedicated folder under `registry/`
- Files are prefixed with `calldata-` for smart contract metadata or `eip712-` for EIP-712 message metadata
- Common/shared files use descriptive names without prefixes (e.g., `common-AggregationRouterV4.json`)
- Optional `tests/` subdirectories contain sample data for validation

**Key File Types:**
- **Calldata files**: Define clear signing metadata for smart contract functions
- **EIP-712 files**: Define clear signing metadata for typed structured data messages
- **Common files**: Shared definitions that can be included by other metadata files
- **Schema file**: `specs/erc7730-v1.schema.json` validates all metadata files

## Working with ERC-7730 Files

**Metadata Structure:**
- `context` - Binding constraints (contract deployments, ABIs, EIP-712 schemas)
- `metadata` - Constant values and entity information
- `display` - Field formatting and display rules

**Common Patterns:**
- Files reference the schema: `"$schema": "../../specs/erc7730-v1.schema.json"`
- Includes mechanism: `"includes": "common-file.json"` for shared definitions
- Path expressions: Use JSON path syntax to reference data fields
- Format types: `tokenAmount`, `addressName`, `raw`, `date`, `enum`, etc.

**Path Expression Types:**
- `#.` - References to structured data fields (from ABI/message)
- `$.` - References within the ERC-7730 file itself
- `@.` - References to container values (transaction/message envelope)

## Validation Process

All metadata files must validate against the JSON schema at `specs/erc7730-v1.schema.json`. This ensures:
- Correct structure and required fields
- Valid format parameters
- Proper context binding constraints
- Consistent field naming and types

The CI/CD pipeline automatically validates changed descriptor files on pull requests using `erc7730 lint` with GitHub Actions integration.

**GitHub Actions Workflows:**
- `pull_request.yml` - Validates changed descriptor files on PRs
- `format.yml` - Daily automated formatting of all descriptors

## Development Guidelines

**File Naming:**
- Use descriptive, consistent names that include contract/protocol versions
- Follow the prefix convention: `calldata-` or `eip712-`
- Use kebab-case for file names

**Content Requirements:**
- Include complete deployment information (chainId, address)
- Provide clear, user-friendly field labels
- Specify appropriate format types for each field
- Include required fields in the display section
- Document excluded fields that should not be shown

**Quality Standards:**
- Validate all files against the schema before submission
- Test with sample transactions/messages when possible
- Ensure metadata accuracy matches actual contract behavior
- Keep format definitions concise and user-focused

## Common Tasks

**Adding New Metadata:**
1. Create entity folder if it doesn't exist under `registry/`
2. Add metadata file with appropriate prefix (`calldata-` or `eip712-`)
3. Include all required context binding information
4. Define clear display formatting rules
5. Validate against schema using `erc7730 lint`
6. Optionally add test samples in `tests/` subdirectory

**Updating Existing Metadata:**
1. Maintain backward compatibility when possible
2. Update deployment addresses for new contract versions
3. Add new function/message formats as needed
4. Update display rules for clarity
5. Run `erc7730 format` to ensure consistent formatting

**Schema Validation:**
- Use `erc7730 lint` command for validation
- Reference examples in existing registry files
- Test with actual transaction/message data
- Check schema at `specs/erc7730-v1.schema.json` for field requirements

**Pull Request Requirements:**
- PR must only modify one entity folder
- Entity folder name should match submitter's organization
- All files must pass `erc7730 lint` validation
- Include descriptive commit messages explaining changes

## Resources

**Specification and Templates:**
- Full specification: `specs/erc-7730.md`
- JSON schema: `specs/erc7730-v1.schema.json`
- Example templates: `specs/templates/`
- Standard ERC interfaces: `ercs/` directory

**External Resources:**
- [Ledger Developer Portal](https://developers.ledger.com/docs/clear-signing/erc7730) - Official documentation
- [ERC-7730 Standard](https://eips.ethereum.org/EIPS/eip-7730) - Ethereum Improvement Proposal

This registry serves as a critical security infrastructure component, enabling users to understand transaction details clearly before signing.