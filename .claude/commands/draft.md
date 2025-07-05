---
allowed-tools: Bash, WebFetch(domain:raw.githubusercontent.com)
description: Draft metadata for a contract address based on Etherscan API data
---

## TL;DR

Draft metadata per the ERC-7730 standard for a specific contract on an EVM blockchain.

## Context

- The user passes $ARGUMENTS which are structured as {chain_id}:{contract_address}
- The Etherscan API key token (`YourApiKeyToken`) can be found in the environment variable $ETHERSCAN_API_KEY (see `.env` first)
- Some valuable Etherscan API endpoints:
  - Get contract ABI: https://api.etherscan.io/v2/api?chainid={chain_id}&module=contract&action=getabi&address={contract_address}&apikey={etherscan_api_key}
  - Get contract source code: https://api.etherscan.io/v2/api?chainid={chain_id}&module=contract&action=getsourcecode&address={contract_address}&apikey={etherscan_api_key}
  - the V2 of the API can handle other chains as well; no need to change the domain, only the chainid variable
- The `erc7730` package can be accessed in the virtual environment through !`pipenv`
- More relevant data for a particular chain (eg name or symbol) can be retrieved from https://github.com/ethereum-lists/chains/tree/master/_data/chains

## Tasks

- If necessary, initiate a virtual environment by using pipenv:
  - use !`pipenv verify` to confirm the environment's status
  - if no environment present, install it
  - run the `--help` flag on `erc7730` to confirm the package is accessible
- Validate the user input by calling the Etherscan API and confirming there is a verified contract at that address. Use HTTP GET to call the API
- Bootstrap a new descriptor JSON file with `erc7730 generate`. Let it grab the ABI from Etherscan by itself
- Given the ERC-7730 specs (https://github.com/lcastillo-ledger/ERCs/blob/master/ERCS/erc-7730.md) and all example descriptor JSON files in `registry/`, make sure all the keys and values in the `display.formats` section of the new description JSON file are correct, complete and clear for any novice user to understand. Be sure to give each function an `"intent"` field
- Lint and format it using the `erc7730` package until they both pass without any problems
- Add a CLAUDE_REPORT.md file in the registry's subdirectory with your findings, including tasks left for the user or other issues left unsolved. Be sure to mention this new JSON file is a draft and meant as a starting point for the user; manual review is required
