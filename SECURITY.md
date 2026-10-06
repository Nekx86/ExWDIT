# Security Policy

## Intended use

ExWDIT is a **defensive** digital forensics and executable analysis toolkit. It must not be used to create, distribute, or improve malware, nor to bypass, disable, or evade anti-cheat or security products. Contributions that add such functionality will be rejected.

## Safe handling of samples

- Treat every analyzed file as **untrusted**.
- Static analysis does not execute the sample, but parsers can contain bugs. Analyze unknown samples in an isolated environment when possible.
- Run **dynamic analysis only in an isolated environment** (virtual machine or Windows Sandbox) with snapshots, restricted networking, no shared folders, and no personal data.
- Process-level sandboxing is an additional layer and does not replace a VM.

## Reporting a vulnerability

If you find a security issue in ExWDIT itself (for example a crash or memory-safety bug when parsing a malformed PE file), please **do not open a public issue.**

Report it privately through GitHub's **Security → Report a vulnerability** feature on this repository. Include reproduction steps, the affected version or commit, and, if safe to share, a minimal test file.

You can expect an acknowledgment and a status update as development capacity allows.

## Supported versions

ExWDIT is in early development. Only the latest commit on the default branch is supported.
