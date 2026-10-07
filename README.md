
# ChainCert 🔐⛓️

## Blockchain-Based Tamper-Evident Record Verification

ChainCert is a blockchain-based academic certificate verification system designed to detect unauthorized changes to digital academic records.

The project uses **SHA-256 hashing** and **previous-hash linking** to create a simple blockchain. If someone changes a record, its hash changes, causing the blockchain validation to fail and indicating possible tampering.

## 🎯 Problem

Digital academic records can be modified without providing cryptographic evidence of their previous state. This makes independent verification difficult.

For example:

```text
Original: Marks = 85
Modified: Marks = 95
```

Changing the data changes its SHA-256 hash, allowing the system to detect the modification.

## 💡 Solution

ChainCert follows this workflow:

```text
Student Record
      ↓
Create Block
      ↓
SHA-256 Hash
      ↓
Previous-Hash Link
      ↓
Blockchain
      ↓
Verify Record
```

Each block stores:

* Record data
* Timestamp
* Previous block hash
* Current hash

If a block is modified, its hash changes and the next block's previous-hash reference becomes invalid.

## 🚨 Live Demo

The project demonstrates tampering in real time:

```text
Valid Record
    ↓
Change Marks: 85 → 95
    ↓
Hash Mismatch
    ↓
Previous-Hash Mismatch
    ↓
TAMPERING DETECTED
```

An untouched certificate can still be verified as **VALID**.

## 👥 User Roles

### Admin / Issuer

* View records
* Add records
* Edit records
* Verify records

### Student / User

* View records
* Verify records
* Cannot edit or delete records

Authentication and authorization control **who can modify records**, while blockchain validation detects **whether stored data has changed**.

## 🛠️ Technology Stack

* **Python**
* **Flask**
* **SHA-256 / hashlib**
* **HTML**
* **CSS**
* **JavaScript**
* **JSON / SQLite**

## 🏗️ Architecture

```text
Frontend
   ↓
Flask API
   ↓
Python Blockchain
   ↓
SHA-256
   ↓
Chain Validation
   ↓
JSON / SQLite
```

## ⚠️ Current Limitation

The current project is a **single-node educational prototype**. It demonstrates blockchain concepts and tamper detection but is not a fully decentralized production blockchain.

Future versions can include:

* QR certificate verification
* Digital signatures
* Multiple blockchain nodes
* Consensus mechanisms
* University integration
* Mobile verification

## 🔐 Privacy

Detailed personal information can remain **off-chain**, while integrity-related information such as the record hash, record ID, timestamp, and issuer information can be used for verification.

## 🚀 Future Vision

ChainCert aims to make academic certificates easier to verify and provide cryptographic evidence when stored records have been modified.

**Verify once. Detect tampering instantly.**
