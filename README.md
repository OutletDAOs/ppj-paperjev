<div align="center">

# OpenStandard

**Open infrastructure for institutional finance onchain.**

Traditional finance is moving onchain, and the institutions leading that shift need infrastructure they can trust and security they can stand behind.

[Website](#) · [Docs](#) · [Security](SECURITY.md) · [Contributing](#contributing)

</div>

---

## Why OpenStandard

Banks, asset managers, custodians and market infrastructure providers are bringing real assets and real money onchain: tokenized funds, treasuries, deposits, settlement rails. The opportunity is enormous. The tolerance for failure is zero.

Most onchain tooling was built for speed and experimentation. Institutions need something different:

- **Predictability** — behavior that is specified, documented and verifiable before it reaches production.
- **Security as a first principle** — not a final checklist item.
- **Transparency** — open code that risk, compliance and engineering teams can inspect line by line.
- **Interoperability** — standards that work across chains, custodians and counterparties instead of locking anyone in.

OpenStandard exists to provide that foundation, in the open.

## What we're building

| Area | Purpose |
| --- | --- |
| **Core standards** | Open specifications for tokenized assets, permissions and lifecycle events that institutions can adopt and extend. |
| **Reference implementations** | Minimal, well-tested smart contracts and SDKs that implement the standards exactly as written. |
| **Compliance primitives** | Building blocks for identity, allowlisting, transfer restrictions and reporting, designed to fit regulatory requirements rather than work around them. |
| **Security tooling** | Testing, monitoring and verification tools that make it easier to prove a system does what it claims. |

> 🚧 OpenStandard is in active development. Interfaces may change before the first stable release.

## Principles

1. **Open by default.** Code, specifications and design decisions are public.
2. **Minimal surface area.** Less code means fewer places for things to go wrong.
3. **Specify, then build.** Every component starts with a written spec.
4. **Test everything.** Unit, integration, fuzz and invariant testing are standard, not optional.
5. **No silent changes.** Every change that affects behavior is versioned and documented.

## Repository structure

```
openstandard/
├── specs/          # Written specifications for each standard
├── contracts/      # Reference smart contract implementations
├── sdk/            # Client libraries for integrating with OpenStandard
├── tools/          # Testing, verification and monitoring tooling
├── docs/           # Guides and architecture documentation
└── SECURITY.md     # How to report vulnerabilities
```

## Getting started

```bash
git clone https://github.com/<org>/openstandard.git
cd openstandard
```

Setup and build instructions will be published with the first release. Watch the repo to be notified.

## Security

Security is the core of this project. If you believe you've found a vulnerability, **please do not open a public issue.** Follow the process in [SECURITY.md](SECURITY.md) to report it privately.

## Contributing

We welcome contributions from engineers, researchers, auditors and institutions.

1. Open an issue to discuss the change before starting significant work.
2. Fork the repository and create a branch from `main`.
3. Include tests and update the relevant spec or docs.
4. Open a pull request with a clear description of what changed and why.

## License

Released under the [MIT License](LICENSE) unless otherwise noted.

---

<div align="center">
<sub>Built for the institutions bringing finance onchain.</sub>
</div>
