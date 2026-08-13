# Developer Integration Guidelines: Decentralized Infrastructure for Janavani

This document outlines the step-by-step developer guidelines for integrating decentralized protocols, privacy layers, and cryptographic verification mechanisms into the Janavani ecosystem. 

---

## 🗺️ Architectural Roadmap

```
             [ Janavani Frontend Client ]
             /            |             \
    (Identity)       (Network Layer)     (Data & State Verification)
       /                  |                    \
  [ Nostr ]          [ Nym Mixnet ]         [ Blockchain / ZKP ]
                          |
                  [ Reticulum Mesh ]
```

---

## 1. Identity & Communication: Nostr Protocol

### Context & Purpose
Janavani leverages the Nostr (Notes and Other Stuff Transmitted via Relays) protocol to provide censorship-resistant identity profiles (using public keys) and decentralized metadata distribution. This allows citizens to share civic updates without a centralized server database.

### Step-by-Step Implementation
1. **Dependency Integration**: Add the official `nostr-sdk` crate to your application's `Cargo.toml`:
   ```toml
   [dependencies]
   nostr-sdk = "0.34.0" # Use the latest stable version
   tokio = { version = "1.0", features = ["full"] }
   ```
2. **Key Pair Generation**: Generate cryptographic identities locally on the user's client machine.
   ```rust
   use nostr_sdk::prelude::*;

   pub fn generate_citizen_identity() -> Result<Keys> {
       let keys = Keys::generate();
       // Public Key (npub...) is shared publicly; Private Key (nsec...) stays encrypted locally
       Ok(keys)
   }
   ```
3. **Publishing Civic Updates (Kind 1 Event)**:
   ```rust
   pub async fn publish_civic_note(keys: &Keys, message: &str) -> Result<()> {
       let client = Client::new(keys);
       client.add_relay("wss://relay.damus.io").await?;
       client.connect().await;

       // Send an anonymous or signed report as a text note
       client.send_text_note(message, []).await?;
       Ok(())
   }
   ```

### Review Guardrails
* **Never store private keys (`nsec`) on a central server.** All signing operations must happen purely in-browser or inside the local client CLI.
* Implement **NIP-05 hex public key verification** so verified community groups can claim clear identity indicators on the platform.

---

## 2. Network Anonymity: Nym Protocol (Mixnet)

### Context & Purpose
Even with encrypted data, network metadata (IP addresses, timing data) can leak a citizen's physical location. Janavani routes traffic through the Nym Mixnet to obfuscate metadata, protecting users filing sensitive municipal grievances.

### Step-by-Step Implementation
1. **Initialize Nym Client**: Developers must use the `nym-sdk` crate or bundle the standalone `nym-client` daemon alongside the Janavani application binary.
   ```toml
   [dependencies]
   nym-sdk = "0.2.0"
   ```
2. **Establish Mixnet Connection**: Connect the application to a local Nym mixnet proxy gateway.
   ```rust
   use nym_sdk::mixnet;

   #[tokio::main]
   async fn main() -> Result<(), mixnet::MixnetClientError> {
       // Create and start a mixnet client session
       let mut client = mixnet::MixnetClient::connect_new().await?;
       let nym_address = client.nym_address();
       println!("Janavani client identity address: {}", nym_address);
       Ok(())
   }
   ```
3. **Route Traffic via Mixnet**: Format data payloads as Sphinx packets to send messages across mixnet nodes before hitting the target endpoint.
   ```rust
   let recipient = mixnet::Recipient::try_from_base58_string("TARGET_NYM_ADDRESS")?;
   client.send_str(recipient, "Civic complaint payload data").await?;
   ```

### Review Guardrails
* Account for network latency; due to the multi-layered mixing of packets, responses will experience a deliberate delay of several hundred milliseconds.

---

## 3. Off-Grid Resilience: Reticulum Network

### Context & Purpose
When internet access is unstable or actively disrupted, Janavani relies on Reticulum to establish local peer-to-peer data transport over alternative mediums (mesh radio, LoRa, Wi-Fi ad-hoc).

### Step-by-Step Implementation
1. **Configure Interface**: Developers must establish a programmatic interface with a running local Reticulum daemon (`rnsd`).
2. **Define Destinations**: Establish an open announcement channel or private inbound paths for municipal complaint forms.
   ```python
   import RNS

   # Initialize Reticulum Stack
   RNS.Reticulum()

   # Establish an application identity
   app_identity = RNS.Identity()
   destination = RNS.Destination(
       app_identity,
       RNS.Destination.IN,
       RNS.Destination.SINGLE,
       "janavani",
       "civic_intake"
   )
   ```
3. **Data Transport Execution**: Define clear data delivery validation callbacks to verify data delivery status across low-bandwidth links without high protocol overheads.

### Review Guardrails
* Enforce **strict packet size limits**. Reticulum links are built for efficiency; optimize report templates to omit heavy asset tracking, transmitting raw textual arrays and compact coordinates instead.

---

## 4. Privacy-Preserving Proofs: Zero-Knowledge Proofs (ZKP)

### Context & Purpose
ZKPs enable citizens to prove eligibility (e.g., "I am a registered resident of this municipality") without revealing their name, phone number, or exact address. This protects whistleblowers reporting systemic public-sector irregularities.

### Step-by-Step Implementation
1. **Select Proving Framework**: Leverage `arkworks` or `bellman` circuits to construct a zero-knowledge configuration pipeline.
2. **Define the Circuit Statement**: 
   * **Secret Inputs (Witness)**: Citizen ID Number, Personal Identity Signature.
   * **Public Inputs**: Public Root Hash of the Registered Citizen Merkle Tree.
   * **Output**: Valid proof signature confirming inclusion in the tree.
3. **Verification Profile Setup**:
   ```rust
   // Pseudo-code implementation workflow using a Groth16 circuit structure
   pub fn generate_eligibility_proof(witness: CitizenData, public_inputs: PublicParams) -> Proof {
       let circuit = JanavaniResidenceCircuit { witness, public_inputs };
       let mut rng = ark_std::test_rng();
       let proof = Groth16::<Bls12_381>::prove(&proving_key, circuit, &mut rng).unwrap();
       proof
   }
   ```

### Review Guardrails
* Use extensively audited curves (e.g., **BLS12-381** or **BN254**). 
* Pre-compute circuit parameters where possible; proof generation should be performant enough to execute inside standard client browser extensions or smartphone modules.

---

## 5. Immutable Ledger Verification: Blockchain State Anchoring

### Context & Purpose
To eliminate administrative tampering or unilateral retroactive backdating of public complaints, Janavani anchors cryptographic proof states and verification hashes into a public, decentralized blockchain ledger.

### Step-by-Step Implementation
1. **Hash Generation**: Calculate a unique SHA-256 state hash matching the finalized civic document bundle.
2. **Contract Call Construction**: Construct a secure smart contract write call (e.g., via `ethers-rs` or `alloy` engines) targeting a low-cost, decentralized ledger infrastructure.
   ```rust
   use alloy::providers::{Provider, ProviderBuilder};
   use alloy::sol;

   // Bind to deployed anchoring contract
   sol!(
       #[sol(rpc)]
       interface JanavaniAnchor {
           function anchorDocument(bytes32 documentHash) external;
       }
   );
   ```
3. **Execution Script**: Commit the hash state to the blockchain network to produce an immutable, publicly auditable timestamp.

### Review Guardrails
* **Never write Personally Identifiable Information (PII) to a blockchain ledger.** Only store high-entropy document fingerprint hashes. Blockchains are immutable, and writing PII violates fundamental local privacy provisions and data erasure protections.

---

## 🛠️ Verification Checklist for Core Maintainers
Before approving a pull request merging decentralized stack modules, ensure the implementation passes this check:
- [ ] Cryptographic private keys are completely isolated within client runtimes and zero system logic exposes them to outbound paths.
- [ ] Network failure fallback mechanisms are implemented for situations where local mixnet proxies or mesh nodes are temporarily unresponsive.
- [ ] Document fingerprint verification functions are accompanied by clear tests validating timestamp collision protections.
- [ ] All code modifications strictly conform to the primary data-minimization tenets detailed in the foundational Janavani manifestos.
