# ChainCert - Blockchain-Based Tamper-Proof Record Verification

A blockchain built from scratch in Python that stores certificate and marks records as
hash-linked blocks, with a Flask dashboard where judges edit a block and see exactly which
hash no longer matches. Records are kept in a SQLite database, so they survive a restart.


| # | Feature | Where |
|---|---|---|
| 1 | **Exact tamper detection.** Every failed check shows the expected and actual values side by side, with the differing characters highlighted. The report names the block, the check, and the edited field. | `blockchain.py` `validate_report()`, inspector and report in the dashboard |
| 2 | **Professional dashboard.** Sidebar navigation, statistics row, block explorer, block inspector, validation report, activity log. Text labels and vector icons only, no emojis. | `templates/index.html` |
| 3 | **12 sample certificates** for the live demo. The first is Rahul Verma, 85 marks (CERT001), matching the slides. | `samples.py` |
| 4 | **Persistent storage** in SQLite. Blocks, tampering and the activity log all survive a Flask restart. | `storage.py` |
| 5 | **`requirements.txt`** | `requirements.txt` |



| File | Purpose |
|---|---|
| `blockchain.py` | `Block` and `Blockchain`: SHA-256 hashing, proof of work, linking, detailed validation report |
| `storage.py` | SQLite persistence (blocks, difficulty, activity log) using only the standard library |
| `samples.py` | Fictional sample certificates for the demo |
| `app.py` | Flask REST API and server (`create_app()` factory) |
| `templates/index.html` | Dashboard (single page, no external assets, works offline) |
| `test_blockchain.py` | Core logic and detailed detection tests |
| `test_app.py` | API, sample data and restart persistence tests |
| `chaincert.db` | Created automatically on first run (not shipped) |

## 4. How tamper detection works

Each block stores: index, timestamp, data (the record), field fingerprints, previous hash, nonce, hash.
The block hash is SHA-256 over all of those fields except the hash itself.
`validate_report()` runs four checks on every block:

| Check | Passes when | Catches |
|---|---|---|
| Block hash | stored hash equals the hash recomputed from the content | any edit to the block |
| Field fingerprints | each field matches the SHA-256 fingerprint recorded at creation | **which field** was edited (also added or removed fields) |
| Proof of work | stored hash starts with the required zeros | a hash that was typed in or recomputed without mining |
| Link | the block's `previous_hash` equals the recomputed hash of the previous block | an edit to an earlier block, even if its hash was recomputed |

Block status in the explorer:
- **VALID**: all four checks pass.
- **TAMPERED**: the block's own hash, fields or proof of work fail.
- **BROKEN LINK**: the block itself is fine, but it points to an earlier block that changed.

### What the dashboard shows for a plain edit (judge changes 85 to 95 in block 1)
- Block 1: **TAMPERED**. Check 1 shows the stored hash and the recomputed hash with every differing character highlighted ("63 of 64 characters differ"). Check 2 names the field `marks` and shows its recorded and current fingerprints.
- Block 2: **BROKEN LINK**. Check 4 shows the previous hash stored in block 2 against the actual hash of block 1.
- The Validation Report lists all three failures: Block hash mismatch, Field fingerprint mismatch, Previous hash mismatch.

### The advanced attacker option
Tick "Advanced attacker" in the Simulate tampering panel. The tool then also recomputes the edited block's fingerprints and hash. The block's own hash looks consistent again, but Block 2's link still fails and the new hash does not satisfy proof of work. To hide the edit completely the attacker would have to re-mine every later block, which is the honest limit of a single-node prototype: a real network of many nodes would reject the forged chain.

## 5. API

| Method and path | Purpose |
|---|---|
| `GET /api/chain` | Chain, `valid`, per-block `blocks` checks, flat `problems` list, `summary` |
| `POST /api/add` | Issue a record (mines a block). Body: `record_id`, `student_name`, `course`, `marks`, `issuer`, `issue_date` |
| `POST /api/tamper` | Demo only. Body: `index`, `field`, `value`, optional `rehash` |
| `GET /api/verify/<record_id>` | Authentic or tampered, with the exact issues |
| `POST /api/seed` | Load the 12 sample certificates (skips ones already present) |
| `GET /api/samples` | The sample list |
| `GET /api/events` | Activity log |
| `GET /api/export` | Download the chain as JSON |
| `POST /api/reset` | New chain from a fresh genesis block |

