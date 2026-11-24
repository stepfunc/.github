# Security Policy

This policy and its response targets apply only to the protocol libraries that Step Function I/O licenses
commercially:

- [dnp3](https://github.com/stepfunc/dnp3)
- [rodbus](https://github.com/stepfunc/rodbus)

Both are source-available under a non-commercial license, with separate commercial licenses, and otherwise follow the
norms and expectations of open-source software. We support coordinated vulnerability disclosure using GitHub's
built-in tooling.

Other repositories in this organization are supporting tools and are not covered by the commitments below. You are
still welcome to report a problem in one of them privately.

## Reporting a Vulnerability

Please do **not** open a public issue for potential security problems.

Instead, go to the **Security** tab of the affected repository and click **"Report a vulnerability"** to submit a
private report to the maintainers:

- [Report a vulnerability in dnp3](https://github.com/stepfunc/dnp3/security/advisories/new)
- [Report a vulnerability in rodbus](https://github.com/stepfunc/rodbus/security/advisories/new)

> If the "Report a vulnerability" button is not visible, make sure you're signed in to GitHub.

No PGP or email is required. If you're unsure whether something is a vulnerability, please report it privately anyway.

## What to Expect

For reports submitted through GitHub's private vulnerability reporting:

- We aim to **acknowledge reports within 48 hours**
- We aim to provide **initial triage feedback within 5 business days**
- We aim to resolve confirmed issues within **90 days** (often sooner)

These are non-binding response targets based on community norms. They do **not** constitute a service-level agreement.

For commercial users, your signed support agreement governs any contractual response commitments.

## Supported Versions

We ship security fixes in new releases on the latest minor line of the current major version. Users on earlier minor
lines must upgrade to receive fixes.

For one year after the first stable release of a new major version, we also backport applicable security fixes to the
latest minor line of its predecessor.

## How Vulnerabilities Are Disclosed

We publish confirmed vulnerabilities as
[GitHub Security Advisories](https://docs.github.com/en/code-security/security-advisories) in the affected repository,
and we normally coordinate publication with a fixed release. The release notes for a security fix lead with a link to
the advisory.

- [dnp3 advisories](https://github.com/stepfunc/dnp3/security/advisories)
- [rodbus advisories](https://github.com/stepfunc/rodbus/security/advisories)

We use GHSA IDs and do not request CVE IDs.

## Staying Informed

To stay up to date:

- **Watch the repository**: Click "Watch" → "Custom" → enable "Releases" and "Security alerts"
- Use the
  [GitHub Security Advisories RSS feed](https://github.blog/changelog/2022-10-25-syndicate-github-security-advisories-with-rss/)
- Use your organization's dependency scanners. `cargo audit` alone can miss an advisory that has not reached the
  RustSec database, so don't rely on it as your only source

## How the Libraries Are Hardened

Both Rust protocol cores forbid `unsafe` code. The language bindings use unsafe code at the boundary with the other
language and rely on callers to supply valid pointers and handles. CI denies common panic-prone constructs, and proves
at link time that selected parsing, formatting and framing paths can't panic for any input. Asynchronous session
logic, I/O, logging and application callbacks are outside that proof. Both libraries receive into fixed buffers and
bound the work or queued requests a peer can impose. TLS uses rustls with mutual authentication and only the trust you
configure.

The security page of each library's guide gives the detail, including exactly what the proof covers and what it
doesn't:

- [dnp3 security page](https://docs.stepfunc.io/dnp3/1.8.0/guide/docs/security)
- [rodbus security page](https://docs.stepfunc.io/rodbus/1.6.0/guide/docs/security)

## Supply Chain Security

### Automated Vulnerability Scanning

- **Scheduled audits**: `Cargo.lock` is scanned daily at 02:00 UTC using `cargo audit` and `osv-scanner`, which also
  covers advisories published on GitHub that don't reach the RustSec database
- **Pull request checks**: The same scans run on every pull request before merging
- **Reports**: Each scan's report is kept as a workflow artifact, and a failed `cargo audit` lists the advisories in
  the job summary

### Dependency Management

- **Minimal dependencies**: We strive to minimize external dependencies where practical
- **Regular updates**: Dependencies are regularly reviewed and updated to address known vulnerabilities
- **Lock file commits**: `Cargo.lock` is committed to fix dependency resolution
- **Audit before release**: A release is only tagged once the security scans pass on its release PR

### Build and Release

- **Pinned build tools**: CI pins external GitHub Actions to a commit and the Rust toolchain to a version, and the
  workflows are checked with `zizmor` on pull requests
- **Approved publishing**: Releases publish only from tags, and only after a maintainer approves the run
- **Trusted publishing**: The crates on crates.io and the packages on nuget.org are published by the release tag's
  workflow through trusted publishing
- **Signed Java artifacts**: The jars on Maven Central carry PGP signatures. The jars for dnp3 1.8.0 and rodbus 1.6.0
  were signed with the key whose fingerprint is `DFD7 6A43 C349 DBF5 46AB 9033 C6FB 909E 94CB 1619`

### Software Bill of Materials

Starting with dnp3 1.8.0 and rodbus 1.6.0, releases include [CycloneDX](https://cyclonedx.org/) SBOMs generated from
the Cargo build of the prebuilt bindings:

- `<library>-ffi.cdx.json` is included in the C/C++ zip and the .NET NuGet package
- `<library>-ffi-java.cdx.json` is included in the Java jar

Both files are also attached to the GitHub release. Scan the matching SBOM alongside your application's own
dependencies. If you use the Rust crate directly, audit your application's own `Cargo.lock`.

### Verifying Release Artifacts

Starting with dnp3 1.8.0 and rodbus 1.6.0, the release workflow attests every file it uploads to the GitHub release,
including both SBOMs. The attestation records the workflow run and commit that built the file. Verify a downloaded
file with the GitHub CLI:

```bash
gh attestation verify dnp3-1.8.0.zip --repo stepfunc/dnp3
gh attestation verify rodbus-1.6.0.zip --repo stepfunc/rodbus
```

nuget.org signs each package it accepts, which changes the file, so verify the copy of the NuGet package attached to
the GitHub release.

## Commercial Support

Customers with support agreements receive technical help and guidance but must track public releases using the same
tools available to the open-source community.

Optional security notification services may be introduced in the future.

## Use in Your Own Compliance Work

You can use this policy, our published advisories and our release SBOMs as evidence in your own
vulnerability-management assessments.
