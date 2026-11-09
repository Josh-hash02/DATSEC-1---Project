# SHA-256 Lab (Web Version)

DATSEC1 Group Final Project: Cryptographic Algorithm Exploration, Vibe-Coded Demonstration, and Security Improvement.

**Algorithm:** SHA-256 (cryptographic hash function, SHA-2 family, FIPS 180-4)

A single-page web app that hashes text with SHA-256, shows the algorithm's internal steps, visualises the avalanche effect as a 256-bit grid, and verifies a message against an expected hash.

> Educational use only. For real applications use a vetted library (for example the browser's Web Crypto API). Passwords need Argon2id, bcrypt or PBKDF2, not plain SHA-256.

## Requirements

- Any modern browser (Chrome, Edge, Firefox, Safari)
- No installation, build step, server or `npm install`

## Setup and run

**Option 1: open locally**
1. Download `index.html`.
2. Double-click it to open in your browser.

**Option 2: GitHub Pages (shareable link)**
1. Push this folder to a GitHub repository.
2. Go to Settings, then Pages.
3. Under "Build and deployment", choose "Deploy from a branch", select `main` and `/ (root)`, then Save.
4. After a minute your site is live at `https://<username>.github.io/<repo-name>/`.

## Features

| Section | What it does |
|---|---|
| 1. Hash a message | Computes the SHA-256 digest of any text (up to 2000 characters). Shows byte length and cross-checks the result against the browser's built-in Web Crypto SHA-256. |
| 2. Verify | Paste an expected hash. Reports match, no match, or invalid format. |
| 3. Avalanche effect | Compares two inputs. Draws each digest as a 256-square grid, marks differing bits in red, and counts how many of 256 bits changed. |
| 4. Inside the algorithm | Shows padding (hex), the message schedule W0 to W15, the first 4 of 64 compression rounds, and the output step. |
| 5. Known limits | Summarises three security limitations. |

## Sample test

| Input | Expected SHA-256 |
|---|---|
| `abc` | `ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad` |
| `abcdbcdecdefdefgefghfghighijhijkijkljklmklmnlmnomnopnopq` | `248d6a61d20638b8e5c026930c3e6039a33ce45964ff2167f6ecedd419db06c1` |

The second input is longer than 55 bytes, so it uses two 512-bit blocks. The "Use test vector" button loads `abc`.

Avalanche example: `Hello, SHA-256` vs `Hello, SHA-257` changes roughly half of the 256 bits (about 50%).

## Input validation

- Empty message: shows an error message and clears the results.
- Expected hash must be exactly 64 hexadecimal characters; otherwise an "Invalid format" error is shown.
- Verify asks the user to compute a hash first if none exists.
- Avalanche requires both inputs to be filled in.
- Message length is capped at 2000 characters.
- Text is shown using `textContent` or escaped before display, so typed input is not run as HTML.

## How the algorithm works

1. **Padding:** append `0x80`, zeros, and the 64-bit message length so the total is a multiple of 512 bits.
2. **Message schedule:** expand each 16-word block into 64 words using rotations and shifts.
3. **Compression:** 64 rounds mix eight working variables (a to h) with round constants `K` and schedule words `W`.
4. **Output:** add the result to the running state; the final eight 32-bit words form the 256-bit digest.

The page includes its own JavaScript implementation to show the steps, and compares it with `crypto.subtle.digest("SHA-256")` on every run.

## Security limitations and mitigations

| Limitation | Cause | Mitigation | Residual risk |
|---|---|---|---|
| Fast brute-force of password hashes | SHA-256 is designed to be fast | Argon2id / bcrypt / PBKDF2 with salt | Weak passwords still guessable |
| Length extension attack | Digest exposes internal state | HMAC-SHA256 or SHA3-256 | Key management mistakes |
| Hash is not authentication | No key; anyone can hash modified data | Digital signatures, or hashes over a trusted channel | Compromised key or channel |

## Optional proof of concept

Not included in this web version. A command-line HMAC-SHA256 tamper-rejection demo exists in the separate Python version of this project.

## Libraries and dependencies

- **JavaScript libraries:** none.
- **Browser API:** Web Crypto (`crypto.subtle`), used only to cross-check results.
- **Font:** Google Fonts (Instrument Sans, JetBrains Mono), loaded if online. If offline, the page falls back to system fonts and works the same.
- **Secrets:** none. Hashing uses no keys, and nothing is stored or sent anywhere. All computation runs in the browser.

## Project structure

```
sha256-lab-html/
├── index.html   # the whole app (HTML, CSS and JavaScript)
└── README.md
```

## References

- NIST FIPS 180-4, Secure Hash Standard
- RFC 2104, HMAC
