# Cargo-Ecosystem-Monitor

Rust ecosystem analysis, mainly the Cargo ecosystem.

Our first target issue is the **Rust unstable feature (RUF)**, published in [ICSE'24](https://dl.acm.org/doi/10.1145/3597503.3623352). See  [released full paper here](./RustUnstableFeature_ICSE24.pdf).

**WIP**: Though our repo follows a decoupled design, the code directory becomes more and more complex thus making reuse a big problem. We are now trying to separate each project into a separate repo for simplicity. 

## Motivation

We focus on the research problem: Are there any security issues that have spread through dependencies across the ecosystem? We choose Rust/Cargo ecosystem as our target, as Rust highlights its security development along the way.

Actually, we divide it into two parts:
- Propagation: **Package granularity**. Mainly focus on the Rust package manager. Typically, we won't analyze any codes inside the package. So the research results only present an overview of the whole ecosystem. Our project for this part is called "Cargo Ecosystem Monitor".
- Reachability and Triggerbility Detection(Pending): In this case, we must statically(main form) or dynamically analyze the **codes** inside crates**.
  - Function revocation: Are there any specific bugs in one crate invoked by other crates through dependencies?
  - Codes clone: In some ways, we have to manually copy+paste others' codes as we want to modify them, which bypasses Cargo. This may cause delay update of related codes and they are often hard to maintain. Example: https://users.rust-lang.org/t/dependency-conflict/61807/5
  - Unsafe <-> Safe function: Can the goalkeeper function make itself safe? It needs propagation, type safety and other types of analysis.
  - Untrusted maintainer: These maintainers can insert vulnerabilities into their packages and then affect other packages through dependencies.

Now, we only focus on the "propagation" which is much easier and more fundamental. After we construct the ecosystem dependency graph, we can dive into the ecosystem to discover more ecosystem-scale impacts from different dimensions.



### Rust Unstable Feature Analysis (Published in ICSE'24)

Our first target issue in our research is the **Rust unstable feature (RUF)**. We observe that the compiler allows developers to use RUF to extend the functionalities of the compiler. However, RUF may introduce vulnerabilities to Rust packages. Moreover, removed RUF will make packages using it suffer from compilation failure, thus breaking their usability and reliability. Even worse, the compilation failure propagates through package dependencies, causing potential threats to the entire ecosystem. Although RUF is widely used by Rust developers, unfortunately, to the best of our knowledge, its usage and impacts on the whole Rust ecosystem have not been studied so far.

To fill this gap, we conduct the first in-depth study to analyze RUF usage and impacts on the whole Rust ecosystem. More specifically, we first extract RUF definitions from the compiler and usage from packages. Then we resolve all package dependencies for the entire ecosystem to quantify the RUF impacts on the whole ecosystem.

By resolving the above challenges, we design and implement RUF extractor to extract all RUF definitions and configurations. 
We identify the semantics of RUF configuration defined by developers for precise RUF impact analysis.
To quantify RUF impacts over the whole Rust ecosystem, we define factors that affect impact propagation and generate a precise EDG for the entire Rust ecosystem (2022-08-11).

We analyzed all Rust compiler versions and obtained 1,875 RUF. We further analyzed all packages on the official package database crates.io and resolve d592,183 package versions to get 139,525,225 direct and transitive dependencies and 182,026 RUF configurations. 

Our highlighted findings are: 1) About half of RUF (47\%) are not stabilized in the latest version of the Rust compiler;
2) Through dependency propagation, RUF can impact 259,540(44\%) package versions, causing at most 70,913 (12\%) versions suffer from compilation failure. To mitigate wide RUF impacts, we further design and implement the RUF compilation failure recovery tool that can recover up to 90% of the failure. We believe our techniques, findings, and tools can help to stabilize the Rust compiler, ultimately enhancing the security and reliability of the Rust ecosystem.
