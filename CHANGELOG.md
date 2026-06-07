# Changelog

All notable changes to this project will be documented in this file.

## [matrix-core] - 2026-06-07

### Added

-  Write path — fixed at the root
The old returns error on success hack is gone. The wallet.Signer interface now takes a TxIntent and returns a SignResult{RawTx, TxHash, From}, so a remote wallet can hand back either a signed tx or a hash instead of smuggling it through an error.

evm.go — new BuildTx builds a real nonce + EIP-1559 fees (legacy fallback) + gas estimate. Kills the nonce=0 / hardcoded-3M issues. Deleted the old BuildDeployTx (it set To to the zero address on a creation tx — a latent bug).
deployer.go — intent-based build, SignResult broadcast handling, and real CREATE2 broadcast to the factory. Fixed the CREATE2 address bug: crypto.CreateAddress2 needs the keccak hash of init code, not the raw init code.
engine.go — Call broadcast is now implemented, and the Gate is actually wired into the engine (it was always nil before).
2. Security model — the clean split, implemented
wallet.go — LocalSigner (self-host) with all three modes: env / keystore (geth v3) / raw. Gate.Authorize now verifies the capability token against named [policy.*] profiles (spend cap, contract + chain allow-lists) — previously ignored.
embedded.go — EmbeddedSigner reuses the validated agent-native DID lane in Go: ed25519 seed → did:matrix:<label>:<keyfp> → challenge/verify → /v1/agent/sign. No local keys.
server.go — mode-aware Bearer auth middleware (constant-time compare), loopback-default with a startup warning when no token is set.
kvx.go + config.go — the new tachyon.config.kvx parser and config model (env > kvx > defaults), with  interpolation so keys never need to live in the file.
Verification
make ci → exit 0: go build + go vet + go test ./... + tachyond --selftest + forge build/smoke all pass.
New tests: kvx parser/precedence, wallet policy/authorize/signer-address/embedded-DID, and the auth middleware. internal/wallet went from zero tests to covered.
Docs/scaffolding: tachyon.config.kvx.example, updated .env.example, .gitignore (ignores real tachyon.config.kvx + key material), and a README Wallet & config section.

Note: go mod tidy pulled in fsnotify + google/uuid (transitive deps of go-ethereum's keystore package) — that's the only dependency change.

### Changed

-  20260606T111050
- is chore: Docker CI fixes, binary release workflow

- .github/workflows/release.yml: new workflow — builds all 8 binaries for
  linux/darwin × amd64/arm64, SHA256 checksums, GitHub Release on v* tag push
-  chore: license headers, gitignore hardening, Docker CI fixes, binary release workflow

- Add Paxlabs copyright header + footer to all .go / .mtx / .kvx source files
- Tighten .gitignore: research/**, executor/bin/, executor/cmd/**/mcl-execute,
  deploy/deploy.sh, fix runs/ conflict, add .gitkeep exceptions for all /** dirs
- Add licenses/ directory: Apache-2.0, MIT, BSD-3-Clause, BSD-2-Clause texts + README
- deploy/daemon/Dockerfile: add go mod download before go build (CI clean-cache fix)
- deploy/gateway + router Dockerfiles: add mkdir -p /out before go build
- .github/workflows/release.yml: new workflow — builds all 8 binaries for
  linux/darwin × amd64/arm64, SHA256 checksums, GitHub Release on v* tag push
-  first commit

### Fixed

-  fix: restore executor/cmd source dirs accidentally gitignored

executor/cmd/**/mcl-execute matched the source directories themselves,
not just built binaries. Remove the pattern; executor/mcl-execute and
executor/bin/ already cover the stray-binary cases.
-  fix: restore executor/cmd source dirs accidentally gitignored

executor/cmd/**/mcl-execute matched the source directories themselves,
not just built binaries. Remove the pattern; executor/mcl-execute and
executor/bin/ already cover the stray-binary cases.

## [matrix-core] - 2026-06-07

### Added

-  Write path — fixed at the root
The old returns error on success hack is gone. The wallet.Signer interface now takes a TxIntent and returns a SignResult{RawTx, TxHash, From}, so a remote wallet can hand back either a signed tx or a hash instead of smuggling it through an error.

evm.go — new BuildTx builds a real nonce + EIP-1559 fees (legacy fallback) + gas estimate. Kills the nonce=0 / hardcoded-3M issues. Deleted the old BuildDeployTx (it set To to the zero address on a creation tx — a latent bug).
deployer.go — intent-based build, SignResult broadcast handling, and real CREATE2 broadcast to the factory. Fixed the CREATE2 address bug: crypto.CreateAddress2 needs the keccak hash of init code, not the raw init code.
engine.go — Call broadcast is now implemented, and the Gate is actually wired into the engine (it was always nil before).
2. Security model — the clean split, implemented
wallet.go — LocalSigner (self-host) with all three modes: env / keystore (geth v3) / raw. Gate.Authorize now verifies the capability token against named [policy.*] profiles (spend cap, contract + chain allow-lists) — previously ignored.
embedded.go — EmbeddedSigner reuses the validated agent-native DID lane in Go: ed25519 seed → did:matrix:<label>:<keyfp> → challenge/verify → /v1/agent/sign. No local keys.
server.go — mode-aware Bearer auth middleware (constant-time compare), loopback-default with a startup warning when no token is set.
kvx.go + config.go — the new tachyon.config.kvx parser and config model (env > kvx > defaults), with  interpolation so keys never need to live in the file.
Verification
make ci → exit 0: go build + go vet + go test ./... + tachyond --selftest + forge build/smoke all pass.
New tests: kvx parser/precedence, wallet policy/authorize/signer-address/embedded-DID, and the auth middleware. internal/wallet went from zero tests to covered.
Docs/scaffolding: tachyon.config.kvx.example, updated .env.example, .gitignore (ignores real tachyon.config.kvx + key material), and a README Wallet & config section.

Note: go mod tidy pulled in fsnotify + google/uuid (transitive deps of go-ethereum's keystore package) — that's the only dependency change.

### Changed

-  20260607T161537
-  20260607T150830
-  20260606T111050
-  chore: Docker CI fixes, binary release workflow

- .github/workflows/release.yml: new workflow — builds all 8 binaries for
  linux/darwin × amd64/arm64, SHA256 checksums, GitHub Release on v* tag push
-  chore: license headers, gitignore hardening, Docker CI fixes, binary release workflow

- Add Paxlabs copyright header + footer to all .go / .mtx / .kvx source files
- Tighten .gitignore: research/**, executor/bin/, executor/cmd/**/mcl-execute,
  deploy/deploy.sh, fix runs/ conflict, add .gitkeep exceptions for all /** dirs
- Add licenses/ directory: Apache-2.0, MIT, BSD-3-Clause, BSD-2-Clause texts + README
- deploy/daemon/Dockerfile: add go mod download before go build (CI clean-cache fix)
- deploy/gateway + router Dockerfiles: add mkdir -p /out before go build
- .github/workflows/release.yml: new workflow — builds all 8 binaries for
  linux/darwin × amd64/arm64, SHA256 checksums, GitHub Release on v* tag push
-  first commit

### Fixed

-  Update Minor Bug fix
-  Minor Big fix
-  New internal/abienc package — the core piece. JSON args can't be fed straight to go-ethereum's packer (it needs exact Go types: *big.Int for uint256, common.Address for address, etc.). abienc coerces each JSON value into the precise type the ABI demands, driven by the contract ABI:

ints: decimal or 0x-hex strings (or JSON numbers); exact int8/16/32/64 vs *big.Int per t.Size
address, bool, string, bytes, bytesN/bytes32, dynamic+fixed arrays, and tuples (ordered array or name-keyed object)
exposes Pack(abi, method, args) and PackConstructorArgs(abi, args)
engine.Call() now resolves calldata via callData()/resolveABI():

method+args set → encode (ABI from inline abi, else registry artifact by contract+project_id)
otherwise → pre-encoded hex data (unchanged)
applies to both simulate_only and broadcast paths
call.go — CallRequest gained ABI/Contract/ProjectID. All three transports (REST/RPC/MCP) decode CallRequest generically, so the new fields flow with no transport changes; CLI call reads JSON stdin (no change).
-  fix: restore executor/cmd source dirs accidentally gitignored

executor/cmd/**/mcl-execute matched the source directories themselves,
not just built binaries. Remove the pattern; executor/mcl-execute and
executor/bin/ already cover the stray-binary cases.
-  fix: restore executor/cmd source dirs accidentally gitignored

executor/cmd/**/mcl-execute matched the source directories themselves,
not just built binaries. Remove the pattern; executor/mcl-execute and
executor/bin/ already cover the stray-binary cases.
