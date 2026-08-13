# Developer Guidelines: Building & Publishing Freenet Crates for Janavani

This document details how developers can build decentralized Freenet protocol components (Contracts and Delegates) and publish them to [crates.io](https://crates.io).

---

## 1. Local Toolchain Setup
To compile Rust libraries to WebAssembly for the Freenet runtime, add the target architecture and install the official core developer CLI utilities:

```bash
# Add the required WASM target compiled by the Freenet kernel
rustup target add wasm32-unknown-unknown

# Download and compile the core Freenet binaries from source
git clone https://github.com
cd freenet-core
cargo install --path crates/core   # Installs the local node daemon `freenet`
cargo install --path crates/fdev   # Installs the developer utility tool `fdev`
```

---

## 2. Project Architecture & Cargo Setup
When writing a crate intended for crates.io deployment, format your library `Cargo.toml` to support standard dynamic library builds:

```toml
[package]
name = "janavani-freenet"
version = "0.1.0"
edition = "2021"
description = "A decentralized citizen-governance bridge for Freenet protocol components"
license = "MIT OR Apache-2.0"

[lib]
crate-type = ["cdylib", "rlib"] # Required for WASM compilation hooks

[dependencies]
freenet-stdlib = "0.8"
freenet-scaffold-macro = "0.2"
serde = { version = "1.0", features = ["derive"] }
```

---

## 3. Implementing Core Primitives
Freenet applications rely on two distinct components depending on your execution context:

### A. Contracts (Shared State Network Layer)
Contracts control public data or "shared state". They define valid state layouts and run across untrusted network peers. Use the composable macro to establish automatic conflict-free replication:

```rust
use freenet_scaffold_macro::composable;
use serde::{Deserialize, Serialize};

#[composable]
#[derive(Serialize, Deserialize, Clone, Default, PartialEq, Debug)]
pub struct CitizenGrievanceState {
    pub problem_description: String,
    pub targeted_office: String,
    pub evidence_hash: Vec<u8>,
}
```

### B. Delegates (Local Trust Zone Layer)
Delegates function exclusively inside the local Freenet Kernel on the citizen's local device. They manage secret state data, keep cryptographic elements protected, and sign messages locally without network exposure.

---

## 4. Local Simulation & Tests
Before publishing your library packages publicly, use the test network harness to verify operational execution flows locally:

```bash
# Build the contract target artifact
fdev build

# Run local interaction sandboxes to verify contract states
cargo test
```

---

## 5. Publishing to Crates.io
1. Log in to [crates.io](https://crates.io) using your GitHub Profile.
2. Navigate to **Account Settings** -> **API Tokens** and generate a new token.
3. Authenticate your local machine terminal:
   ```bash
   cargo login <your_secret_api_token>
   ```
4. Verify package completeness and upload your live library crate to the community index:
   ```bash
   cargo package
   cargo publish
   ```
