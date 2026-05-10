# VeriChain

**Provenance for the Post-AI Internet.**

VeriChain is a proposed cryptographic provenance layer for human-generated content. Submitted to HackDavis 2026.

---

## Links

🔗 **Live UX Demo:** https://veri-flow-dash.lovable.app

🔗 **Slide Deck:** https://gamma.app/docs/VeriChain-h95p3yisa16g8ha

---

## The Problem

The cost of fabricating evidence — photos, video, text, audio — has collapsed to near zero. The cost of defending against it has not.

Every system that runs on "show me proof" is quietly breaking:

- E-commerce return fraud with AI-generated damage photos
- Insurance claims with synthetic incident imagery
- Freelance portfolios containing work never made
- Academic submissions written entirely by language models
- Coordinated review-bombing of small businesses with fabricated photos
- Synthetic evidence in journalism, hiring, and courts

Most institutions affected by this do not yet have the vocabulary for what is happening to them.

---

## Our Thesis

**AI detection is a losing arms race.**

The adversary controls the content. Every detector is beaten by the next model six months later.

The durable answer is to invert the problem:

> Don't detect what's fake. Prove what's real.

Provenance is a key-management problem, not a detection problem. Cryptography does not get worse as generative models improve.

---

## How VeriChain Works

**CAPTURE —** A lightweight client signs content at the moment of creation. Photo, video, audio, and text are hashed and signed with a device-bound key (Ed25519). On supported devices, the key is bound to the secure enclave.

**ANCHOR —** Signatures are anchored to a tamper-evident append-only log structured as a Merkle tree. The log is auditable without exposing user content — only hashes are public, never the content itself.

**VERIFY —** Platforms query a public verification API and receive one of three trust signals:
- ✅ `Verified Capture` — signed at origin, untampered
- ⚠️ `Edited After Capture` — signed but modified since
- ❌ `Unverifiable Origin` — no provenance signature detected

---

## Built on an Open Standard

VeriChain extends **C2PA** (Coalition for Content Provenance and Authenticity), already shipping in:

- Adobe Photoshop and Lightroom
- Sony Alpha cameras
- Leica M11
- BBC and the New York Times

C2PA today covers high-end capture devices. VeriChain pushes the same primitive to the long tail — every browser, every phone, every web form, for everyone who doesn't own a $4,000 camera but still needs to prove what's real.

---

## The Demo

The live demo is a customer service dashboard for a fictional e-commerce company.

Two complaints arrive. One is signed at capture — verified in milliseconds, auto-approved. The other has no provenance signature. We generated it with Gemini in eleven seconds. The support rep never has to play AI detective.

We didn't detect the fake. We just noticed the real one was missing.

→ **Try it:** https://veri-flow-dash.lovable.app

---

## What This Submission Is

This submission is a **rigorous concept design with a working UX demo**, not a shipped product.

We made this choice deliberately. The cryptographic primitives VeriChain relies on already exist — Ed25519 signatures, Merkle trees, the C2PA libraries. Rebuilding them in 24 hours produces a worse version of solved work. The contribution here is the framing: **provenance over detection**, and extending C2PA to the long tail.

---

## What's Next

- Reference implementation of the capture client and verification API on top of existing C2PA libraries
- Vertical pilots in the four highest-pain domains: e-commerce returns, insurance claims, freelance marketplaces, university honor codes
- Hardware attestation partnerships with phone OEMs for secure enclave key binding
- A free embeddable public verification widget for small platforms

---

## Why This Matters

The 1990s web was insecure by default. We fixed it with cryptographic signatures everyone could verify — HTTPS.

The post-AI internet has a structurally similar problem and needs a structurally similar fix.

The platforms, institutions, and democracies that function well in the next decade will be the ones that can answer *"where did this actually come from?"* instantly and credibly. The ones that can't are going to be overwhelmed.

VeriChain is our proposal for what that answer looks like.

---

*HackDavis 2026 · Create for social good.*
