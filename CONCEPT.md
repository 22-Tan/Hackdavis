# VeriChain — Concept Design Document

## The core problem, precisely stated

Generative AI has made the marginal cost of producing convincing fake evidence
approach zero. This breaks every system that relies on submitted content as proof
of a real-world event.

The problem is not that AI-generated content exists. The problem is that
**origin is now invisible by default.**

When a photo arrives in a support queue, an insurance portal, or a university
submission system, there is currently no standard way to answer:
- Was this captured by a real device at a real moment in time?
- Has it been modified since capture?
- Or did it originate from a generative model?

This invisibility is the vulnerability. VeriChain addresses it directly.

---

## Why detection is the wrong frame

The dominant response to AI-generated content has been detection — classifiers
trained to distinguish real from synthetic.

This approach has a structural flaw: **the adversary controls the content.**

Every detector is trained on outputs from current models. Every new model
produces outputs that defeat current detectors. The defender is permanently
reactive. The arms race has no stable equilibrium.

More importantly, detection gets *harder* as models improve, not easier.
The trajectory of generative AI guarantees that detection-based systems
become less reliable over time.

---

## The provenance inversion

VeriChain inverts the frame entirely.

Instead of asking "is this content fake?" we ask "can this content prove it's real?"

This is a fundamentally different — and more tractable — problem.

Proving origin is a **key-management problem**. The content is signed at the
moment of capture with a device-bound private key. The signature is anchored
to a public, tamper-evident log. Verification is a cryptographic operation,
not a classification.

Critically: **cryptography does not get worse as generative models improve.**
An Ed25519 signature from 2026 is just as verifiable in 2030. A GAN that
produces photorealistic images in 2030 cannot forge a 2026 signature without
the private key.

The arms race disappears.

---

## The three-component architecture

### 1. Capture client

A lightweight signing client that operates at the moment of content creation:

- **Browser extension** for web-based submission flows
- **Mobile SDK** for native app integration
- **Web capture page** for platforms that can't integrate an SDK

At capture, the client:
1. Computes a SHA-256 hash of the content
2. Signs the hash with a device-bound Ed25519 private key
3. Attaches metadata: timestamp, device class, optional geolocation
4. Submits the signed certificate to the anchor service

The private key never leaves the device. On supported hardware (iOS Secure
Enclave, Android StrongBox), it cannot be exported even by the operating system.

### 2. Anchor service

A tamper-evident append-only log that stores provenance certificates.

Structure: a Merkle tree where each leaf is a signed certificate. The root
hash is periodically published to a transparent, auditable ledger.

Properties:
- **Privacy-preserving:** only content hashes are stored, never content itself
- **Tamper-evident:** any modification to historical entries changes the root hash
- **Auditable:** any third party can verify the log's integrity without seeing user content
- **Fast:** verification is O(log n) — milliseconds at scale

### 3. Verification API

A public REST endpoint. Input: a content hash. Output: a provenance certificate
with one of three trust signals:

| Signal | Meaning |
|---|---|
| ✅ Verified Capture | Signed at origin, chain intact, untampered |
| ⚠️ Edited After Capture | Signed at origin, but content modified since |
| ❌ Unverifiable Origin | No provenance signature on record |

`Unverifiable Origin` does not mean fake. It means the content cannot prove
it's real. The platform decides what to do with that signal — auto-approve,
route to review, or reject. VeriChain surfaces the signal; policy is the
platform's decision.

---

## The C2PA relationship

The Content Authenticity Initiative's C2PA standard (ISO/IEC 23000-22) is the
correct open standard for content provenance. Adobe, Sony, Leica, the BBC, and
the New York Times are already shipping C2PA-compliant tools.

VeriChain is not a competing standard. It is an **extension of C2PA to the
long tail.**

C2PA today primarily covers high-end capture devices and professional creative
tools. The standard exists and is sound. The coverage gap is everything else —
every browser, every phone camera outside the C2PA ecosystem, every web form,
every customer submitting evidence through a support portal.

VeriChain fills that gap using the same cryptographic primitives C2PA defines,
ensuring interoperability with the growing C2PA ecosystem.

---

## Attack surface analysis

**Q: What if someone screenshots a real signed photo and re-submits it?**

The screenshot has a different SHA-256 hash than the original. The provenance
certificate is bound to the original hash. Verification returns `Unverifiable
Origin` for the screenshot.

**Q: What if someone modifies an image slightly to evade hash matching?**

Any modification — even a single pixel — changes the hash. The certificate
becomes invalid. Verification returns `Edited After Capture`.

**Q: What if someone steals the private key from a device?**

On devices with hardware security modules (Secure Enclave, StrongBox), the key
cannot be exported. On software-only implementations, key theft is a risk —
mitigated by key rotation, revocation lists, and rate limiting on certificates
per key.

**Q: What if someone builds a custom device that signs arbitrary content?**

This raises the cost of fraud from free (generate an image) to expensive
(build custom hardware, manage keys, avoid revocation). That cost increase
is the entire value proposition. We cannot make fraud impossible; we make it
expensive enough to be detectable at scale.

---

## Highest-priority verticals

| Vertical | Pain point | Why provenance fits |
|---|---|---|
| E-commerce returns | AI-generated damage/defect photos | Every complaint has a capture moment |
| Insurance claims | Synthetic incident imagery | Claims are evidence-based by design |
| Freelance marketplaces | Portfolio fabrication | Work has a creation moment |
| Academic integrity | AI-written submissions | Documents have an authorship moment |

---

## What a reference implementation requires

1. Ed25519 key generation and signing — available in all major crypto libraries
2. SHA-256 content hashing — standard
3. A Merkle tree append-only log — well-understood data structure
4. A REST verification API — straightforward
5. A browser extension with capture hooks — ~2 weeks of engineering
6. A mobile SDK wrapping platform crypto APIs — ~4 weeks per platform

The C2PA reference implementation (c2pa-rs, c2pa-js) provides a significant
head start on items 1–4.

This is not a research problem. It is an engineering and adoption problem.

---

*VeriChain · HackDavis 2026*
