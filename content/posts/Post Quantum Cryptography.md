---
title: Securing Browsers in Post Quantum era
date: 2024-12-22
draft: false
tags:
- Browsers
- Cryptography
- Quantum Computer
- Quantum Safe
- Safe Infra
---
## The Quantum Challenge

Browser security isn't just about implementing new algorithms – it's about rethinking our entire security architecture from the ground up.

!![Image Description](/images/Pasted%20image%2020241222163152.png)
### Modern Browser Security

Today's browsers rely heavily on the TLS 1.3 protocol, using elliptic curve cryptography (ECC) for key exchanges and RSA/ECDSA for digital signatures. These mechanisms have served us well, but they share a common vulnerability: susceptibility to Shor's algorithm running on a sufficiently powerful quantum computer.
### Browser's Cryptographic Architecture

#### Network Security Layer

The network security layer handles TLS connections and certificate validation. This layer must be completely redesigned to handle post-quantum algorithms. Current implementations in Chrome and Firefox use OpenSSL or BoringSSL, which are being updated to support post-quantum algorithms [1].

!![Image Description](/images/Pasted%20image%2020241222163250.png)

Key components requiring quantum resistance:

- **Transport Layer Security (TLS):** Modifications to the TLS handshake are necessary to support larger key sizes and new algorithms. CRYSTALS-Kyber, selected by NIST for standardization, introduces significant changes [2]:
    !![Image Description](/images/Pasted%20image%2020241222163526.png)
    
	- Larger public keys (Kyber-768 uses 1184 bytes compared to X25519's 32 bytes)
    - Different error-handling mechanisms for decapsulation failures
    - New key derivation functions optimized for post-quantum security

- **Certificate Processing:** The X.509 certificate infrastructure faces challenges such as:
    
    - Insufficient maximum certificate sizes for post-quantum signatures
    - Longer certificate chains due to larger signatures
    - Increased validation times with post-quantum algorithms

#### Client-Side Cryptographic Operations

Browser-based cryptographic operations present unique challenges. The Web Crypto API, providing cryptographic functionality to web applications, must support post-quantum algorithms while maintaining backward compatibility [3].

Key areas requiring updates:

- **Memory Management:** Post-quantum cryptography demands significantly more memory:

    !![Image Description](/images/Pasted%20image%2020241222163712.png)
    
	- SPHINCS+ signatures can be up to 49KB, compared to 64 bytes for Ed25519
    - Dilithium signatures require around 2.7KB, compared to 64 bytes for Ed25519
    - Key generation necessitates larger entropy pools and secure random number generation

- **Processing Architecture:** Increased computational requirements necessitate new strategies:
    - Parallel processing of hybrid cryptographic operations
    - WebAssembly implementation for performance-critical operations
    - Optimized memory allocation for large keys and signatures

### Understanding the Attack Surface

The quantum vulnerability in browsers extends beyond the obvious TLS connection. Browser security, a layered construct, is threatened at every level by quantum computing.

At the foundational layer, TLS protocol implementations using ECC and RSA/ECDSA are vulnerable to Shor's algorithm [2]. Experts estimate that a quantum computer with several thousand logical qubits could compromise current cryptographic systems [3].

At the certificate processing level, the X.509 infrastructure faces fundamental incompatibility with quantum-resistant algorithms due to size constraints and processing requirements. Secure validation of certificate chains requires quantum-resistant signature verifications throughout.

### The Complexities of Browser Storage

Client-side storage, often overlooked, relies on cryptographic primitives needing quantum resistance. IndexedDB, localStorage, and password managers face challenges such as:

- Deriving encryption keys quickly for smooth user experiences
- Maintaining secure auto-fill without exposing credentials to quantum attacks
- Synchronizing data using quantum-resistant transport mechanisms
- Ensuring backward compatibility with existing websites

### Building a Quantum-Resistant Browser Architecture

Addressing these challenges requires a transitional architecture supporting both classical and post-quantum cryptography. This hybrid approach ensures compatibility with existing web infrastructure while introducing quantum resistance incrementally.

The hybrid cryptographic layer performs parallel key exchanges using classical and post-quantum algorithms, combining results via domain separation techniques. This strategy adds overhead but is essential for a secure transition.

### The Performance Challenge

Post-quantum algorithms, with their larger keys and signatures, significantly impact browser performance. Effective resource management across hundreds of concurrent connections is critical. Strategies include:

- Managing larger keys and signatures without exhausting memory
- Efficient caching of cryptographic operations
- Smart scheduling of verification tasks
- Leveraging hardware acceleration

### Practical Implementation Strategies

A phased approach is necessary for deploying quantum-resistant browsers:

1. Implement hybrid key exchange mechanisms in the TLS stack for immediate quantum resistance while maintaining compatibility.
2. Update certificate chain processing to support hybrid certificates incrementally.
3. Enhance the browser storage security layer with quantum-resistant wrapped keys, enabling smooth transitions without immediate data migration.

### Chromium's Quantum Resistance Efforts

#### Hybrid Approach

Chrome uses a hybrid post-quantum key exchange, combining traditional and quantum-resistant algorithms. This method, detailed in IETF draft-ietf-tls-hybrid-design [5], includes:

- **Key Exchange Implementation:** Parallel use of X25519 (traditional) and Kyber-768 (post-quantum) key exchanges, with shared secrets combined using HKDF [6].
- **Performance Optimization:** Google's analysis shows hybrid key exchanges add only about 0.2 milliseconds of latency per median connection [7].

#### Certificate Processing

Chrome supports hybrid certificates as specified in draft-ietf-tls-hybrid-certificates [8]. Key enhancements include:

![[Pasted image 20241222164838.png]]

- Parallel validation paths for classical and quantum signatures
- Improved memory management for larger certificates
- Optimized chain processing to maintain performance

#### Client-Side Storage Security

Chrome's storage security architecture features:

- Quantum-resistant key derivation functions
- Enhanced encryption for stored credentials
- Hybrid encryption protocols for secure synchronization

### References

[1] Langley, A. (2016). "Experimenting with Post-Quantum Cryptography." Google Security Blog. [https://security.googleblog.com/2016/07/experimenting-with-post-quantum.html](https://security.googleblog.com/2016/07/experimenting-with-post-quantum.html)

[2] Proos, J., & Zalka, C. (2003). "Shor's discrete logarithm quantum algorithm for elliptic curves." Quantum Information & Computation. [https://doi.org/10.48550/arXiv.quant-ph/0301141](https://doi.org/10.48550/arXiv.quant-ph/0301141)

[3] Mosca, M. (2018). "Cybersecurity in an era with quantum computers: Will we be ready?" IEEE Security & Privacy, 16(5), 38-41. [https://doi.org/10.1109/MSP.2018.3761723](https://doi.org/10.1109/MSP.2018.3761723)

[4] Google Security Blog (2022). "Chrome is now using post-quantum encryption." [https://security.googleblog.com/2022/07/chrome-now-uses-post-quantum.html](https://security.googleblog.com/2022/07/chrome-now-uses-post-quantum.html)

[5] Stebila, D., & Mosca, M. (2022). "Hybrid key exchange in TLS 1.3." IETF Draft. [https://datatracker.ietf.org/doc/draft-ietf-tls-hybrid-design/](https://datatracker.ietf.org/doc/draft-ietf-tls-hybrid-design/)

[6] Langley, A. (2022). "Post-quantum cryptography in Chrome." Chromium Blog. [https://blog.chromium.org/2022/07/post-quantum-cryptography-in-chrome.html](https://blog.chromium.org/2022/07/post-quantum-cryptography-in-chrome.html)

[7] Chrome Platform Status (2023). "Post-Quantum Cryptography Implementation Report." [https://chromestatus.com/feature/6678134168485888](https://chromestatus.com/feature/6678134168485888)

[8] IETF (2023). "Hybrid certificates for TLS." Draft-ietf-tls-hybrid-certificates. [https://datatracker.ietf.org/doc/draft-ietf-tls-hybrid-certificates/](https://datatracker.ietf.org/doc/draft-ietf-tls-hybrid-certificates/)

[9] Chromium Security Architecture Documentation (2023). [https://www.chromium.org/Home/chromium-security/security-architecture/](https://www.chromium.org/Home/chromium-security/security-architecture/)

[10] Chromium Design Documents (2023). "Post-Quantum Cryptography Implementation." [https://www.chromium.org/developers/design-documents/](https://www.chromium.org/developers/design-documents/)

[11] NIST (2022). "Post-Quantum Cryptography Standardization." [https://csrc.nist.gov/Projects/post-quantum-cryptography/post-quantum-cryptography-standardization](https://csrc.nist.gov/Projects/post-quantum-cryptography/post-quantum-cryptography-standardization)

[12] Schwabe, P., et al. (2022). "CRYSTALS-Kyber Algorithm Specification v3.02" [https://pq-crystals.org/kyber/data/kyber-specification-round3-20210804.pdf](https://pq-crystals.org/kyber/data/kyber-specification-round3-20210804.pdf)

[13] Cloudflare Research (2023). "Experimenting with Post-Quantum Cryptography." [https://research.cloudflare.com/post-quantum-cryptography/](https://research.cloudflare.com/post-quantum-cryptography/)

[14] Chen, L., et al. (2023). "Report on Post-Quantum Cryptography." NIST Internal Report 8413. [https://doi.org/10.6028/NIST.IR.8413](https://doi.org/10.6028/NIST.IR.8413)

[15] Alagic, G., et al. (2023). "Status Report on the Third Round of the NIST Post-Quantum Cryptography Standardization Process." NIST Internal Report 8413-2023. [https://doi.org/10.6028/NIST.IR.8413](https://doi.org/10.6028/NIST.IR.8413)