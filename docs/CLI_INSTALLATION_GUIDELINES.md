# Developer Guidelines: Automating CLI Distribution & Web Integration

This document outlines the step-by-step setup to integrate the **Janavani CLI** with the frontend website, configure automated builds, and optimize the user onboarding flow.

---

## 1. Web Integration: Interactive Copy-to-Terminal Component
To allow users to opt-in and easily copy the installation commands, add an interactive element to the website.

### Step 1.1: Add the HTML Markup
Insert this container into your `index.html` or preferred layout file:
```html
<div class="cli-install-container" style="border: 1px solid #ccc; padding: 20px; border-radius: 8px; margin: 20px 0;">
    <h3>🛠️ Install Janavani CLI Tooling (Opt-In)</h3>
    <p>Open your local terminal and run the development tool components via Cargo:</p>
    
    <div style="background: #f4f4f4; padding: 10px; border-radius: 4px; display: flex; justify-content: space-between; align-items: center;">
        <code id="cargoCommand">cargo install janavani-cli</code>
        <button onclick="copyCargoCommand()" style="cursor: pointer; padding: 5px 10px;">📋 Copy</button>
    </div>
</div>
```

### Step 1.2: Add the JavaScript Functionality
Include this script block before the closing `</body>` tag to handle the clipboard interaction safely:
```javascript
function copyCargoCommand() {
    const commandText = document.getElementById("cargoCommand").innerText;
    navigator.clipboard.writeText(commandText)
        .then(() => {
            alert("Command copied to clipboard! Paste it into your terminal to install.");
        })
        .catch(err => {
            console.error("Failed to copy command: ", err);
        });
}
```

---

## 2. Cargo Package Optimization
Ensure the Rust binary is optimized for developer compilation and discovery.

### Step 2.1: Update Cargo.toml
In your Rust project directory, ensure `Cargo.toml` contains clean metadata so it compiles effortlessly for end-users:
```toml
[package]
name = "janavani-cli"
version = "0.1.0"
edition = "2021"
description = "A privacy-first citizen governance workflow companion tool."
readme = "README.md"
repository = "https://github.com/netzen-abm/janavani-website"
license = "MIT OR Apache-2.0"

[dependencies]
# Keep dependencies minimal to ensure fast compilation cycles
```

---

## 3. CI/CD: Automated Multi-Platform Binary Releases
Since non-developers may not have a Rust toolchain installed, use GitHub Actions to provide pre-compiled binary fallbacks automatically.

### Step 3.1: Create the Workflow File
Create a new file in your repository at `.github/workflows/release.yml` and add the following content:
```yaml
name: Release Binaries

on:
  push:
    tags:
      - 'v*'

jobs:
  build:
    name: Build for ${{ matrix.os }}
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        include:
          - os: ubuntu-latest
            target: x86_64-unknown-linux-gnu
            artifact_name: janavani-linux
          - os: macos-latest
            target: x86_64-apple-darwin
            artifact_name: janavani-macos
          - os: windows-latest
            target: x86_64-pc-windows-msvc
            artifact_name: janavani-windows.exe

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Install Rust toolchain
      uses: dtolnay/rust-toolchain@stable
      with:
        targets: ${{ matrix.target }}

    - name: Build Binary
      run: cargo build --release --target ${{ matrix.target }}

    - name: Rename Executable
      shell: bash
      run: |
        if [ "${{ matrix.os }}" = "windows-latest" ]; then
          mv target/${{ matrix.target }}/release/janavani-cli.exe target/${{ matrix.target }}/release/${{ matrix.artifact_name }}
        else
          mv target/${{ matrix.target }}/release/janavani-cli target/${{ matrix.target }}/release/${{ matrix.artifact_name }}
        fi

    - name: Upload Binaries to Release
      uses: softprops/action-gh-release@v2
      with:
        files: target/${{ matrix.target }}/release/${{ matrix.artifact_name }}
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

## 4. Maintenance and Best Practices
1. **Semantic Versioning**: Tag your releases strictly using SemVer guidelines (e.g., `git tag v0.1.0` followed by `git push origin v0.1.0`) to trigger the binary compilation workflow safely.
2. **Data Minimization**: Adhere to the platform's core identity principle; the CLI tool must perform local parsing and cryptographic credential management without uploading plaintext user diagnostics.
