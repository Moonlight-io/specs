<p align="center">
  <img
    src="https://assets.moonlight.io/email/2020/Vivid_Logo_0.png"
    height="100px;">
</p>
 
# 1. Version

0.0.2

# 2. DID Method Specification

The Vivid DID method specification conforms to the requirements specified in the DID specification currently published by the W3C Credentials Community Group. For more information about DIDs and DID method specifications, please refer to [DID Primer](https://git.io/did-primer) and [DID Specification](https://w3c-ccg.github.io/did-spec).

## 2.1 Abstract

[**Vivid**](https://vivid.moonlight.io/) is an SSI (Self-Sovereign Identity) solution designed to be platform agnostic. The Vivid solution is optimized for DIDs and Verifiable Credentials. This specification covers how these DIDs are managed and also describes related features.  This document is part of a collection of specifications which outline the Vivid ecosystem.

## 2.2 Vivid DID Method

The namestring that shall identify this DID method is: `vivid`

A DID that uses this method *MUST* begin with the following prefix: `did:vivid:`. Per the DID specification,
this prefix *MUST* be in lowercase. The remainder of the DID, after the prefix, is specified below as the [Method Specific Identifier](#method-specific-identifier).

## 2.3 Method Specific Identifier

The Vivid DID scheme conforms with [the Generic DID Scheme](https://w3c-ccg.github.io/did-spec/#the-generic-did-scheme)
described in the DID spec. The format of the `vivid-specific-idstring` is described below in
[ABNF](https://tools.ietf.org/html/rfc5234):

```
vivid-did = "did:vivid:" vivid-specific-idstring
vivid-specific-idstring = *subnamespace ":" namestring
namestring = 16*128(base58char)
subnamespace = *ALPHA *(ALPHA / DIGIT / "_" / "-")
base58char = "1" / "2" / "3" / "4" / "5" / "6" / "7" / "8" / "9" / "A" / "B" / "C"
    / "D" / "E" / "F" / "G" / "H" / "J" / "K" / "L" / "M" / "N" / "P" / "Q"
    / "R" / "S" / "T" / "U" / "V" / "W" / "X" / "Y" / "Z" / "a" / "b" / "c"
    / "d" / "e" / "f" / "g" / "h" / "i" / "j" / "k" / "m" / "n" / "o" / "p"
    / "q" / "r" / "s" / "t" / "u" / "v" / "w" / "x" / "y" / "z"
```

### 2.3.1 Namestring Generation Method

The `namestring` can be generated in various ways using standard UUID methods and must be a valid base58 character string with length of 16-128.

A convenient regex to match `namestring` is:

```
^([1-9A-HJ-NP-Za-km-z]{16,128})$
```

### 2.3.2 Vivid DID Example

A 33-byte arbitrary compressed public key: `035a928f201639204e06b4368b1a93365462a8ebbff0b8818151b74faab3a2b61a`(hex)

can be represented in base58 as: `znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX`(base58)

An example Vivid DID could be:

```
did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX
```

> *Note:* The assumption that a namestring is a base58 encoded public key is not valid.

## 2.4 Data Structure

Vivid Identity documents use the following data schema to define a platform agnostic authentication solution:

```json
{
  "@context":[
    "https://w3id.org/security/v1"
  ],
  "id":"did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX",
  "type":[
    "identity"
  ],
  "issued":"2020-12-21",
  "authentication":[
    "#neo3",
    "#zilliqa",
    "#neo2"
  ],
  "keyAgreement":[
    "#neo3",
    "#zilliqa",
    "#neo2"
  ],
  "verification":[
    {
      "id":"#neo2",
      "type":"secp256r1",
      "controller":"did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX",
      "publicKeyBase58":"znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX"
    },
    {
      "id":"#neo3",
      "type":"secp256r1",
      "controller":"did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX",
      "publicKeyBase58":"znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX"
    },
    {
      "id":"#zilliqa",
      "type":"secp256k1",
      "controller":"did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX",
      "publicKeyBase58":"25SYHxFftEWxtemFzyy1tWWbGT1ynNVQg88GPuX8YfH1W"
    }
  ]
}
```

# 3. CRUD Operation Definitions

Vivid integrators can interface with any number of contracts on the platforms which Vivid operates on.  DID documents are contained in a collection of smart contracts which share a common interface with varying architecture depending on the ledger offering. These methods are exposed via the [**asteroid-sdk**](http://docs.moonlight.io/) and [**Vivid**](https://vivid.moonlight.io/) application to provide an easy integration experience.

## 3.1 Create

To create a new DID entry, invoke the `CreateIdentity` method in the identity contract of the relevant platform with a valid ID string.

```ts
function CreateIdentity(vividSpecificIDString IDString, privateKey Buffer): undefined
```

Executing this method will create a verification entry for the keypair associated with the privateKey parameter.

> *Note:* A DID record created using this method is be considered **Mandatory** for users of the ecosystem.

## 3.2 Read

Vivid Clients can read a DID document by sending a query request for a DID.

```ts
function ReadIdentity(vividSpecificIDString IDString): DIDDocument
```

### For example:

Querying for `did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX`:

```ts
ReadIdentity("znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX")
```

Resolves:

```json
{
  "@context":[
    "https://w3id.org/security/v1"
  ],
  "id":"did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX",
  "type":[
    "identity"
  ],
  "issued":"2020-12-21",
  "authentication":[
    "#neo3",
    "#zilliqa",
    "#neo2"
  ],
  "keyAgreement":[
    "#neo3",
    "#zilliqa",
    "#neo2"
  ],
  "verification":[
    {
      "id":"#neo2",
      "type":"secp256r1",
      "controller":"did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX",
      "publicKeyBase58":"znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX"
    },
    {
      "id":"#neo3",
      "type":"secp256r1",
      "controller":"did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX",
      "publicKeyBase58":"znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX"
    },
    {
      "id":"#zilliqa",
      "type":"secp256k1",
      "controller":"did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX",
      "publicKeyBase58":"25SYHxFftEWxtemFzyy1tWWbGT1ynNVQg88GPuX8YfH1W"
    }
  ]
}
```

## 3.3 Update

The update operation is used to manage the available verification methods.  For example, a new verification method using polymetric data from an identity's verifiable credentials.

```ts
function UpsertVerification(vividSpecificIDString IDString, verification VerificationObj, privateKey Buffer): undefined
```

This transaction is approved and the verification entry is updated if the invoking privatekey can authenticate against the authentication methods registered for the targeted IDString.
In the case of the Vivid platform, **EVERY** registered verification method is valid for authentication.  It is up to the integrator to determine the level of support.

## 3.4 Delete

The delete operation is used to remove a verification method from a DID document.  This method has the ability to remove **ALL** verification methods from a document **EXCEPT** the 
verification method used to originally create the DID document.  In essence, deleting all available verification methods reverts the DID document to its initial create state.

```ts
function DeleteVerification(vividSpecificIDString IDString, verificationID string, privateKey Buffer): undefined
```

This transaction is approved if the invoking privateKey can authenticate against the authentication methods registered for the targeted IDString.

# 4. Security and Privacy Considerations

Vivid is an open standard designed to be robust against a broad range of ecosystem requirements.  As a result, this solution makes no claims about the security and privacy of platforms that choose to use this DID Method.

It is important for integrators and their users to understand that this method is designed to operate on a collection of immutable DLT platforms.  Once a DID document is initiated on the platform, the data cannot be removed in a non-recoverable way.  All states of a DID document **WILL** be available in perpetuity irrelevant of the use of the `delete` operation due to the intrinsic nature of the platforms which Vivid operates on. 

Due to this behavior, Vivid DID documents should be limited to verification/authentication operations and **SHOULD NOT** contain any Personally-Identifiable Information (PII).  

# 5. Status

This document is a work in progress.

# References

1. Decentralized Identifiers (DIDs) v0.11 https://w3c-ccg.github.io/did-spec
1. ABNF https://tools.ietf.org/html/rfc5234
1. Base58 Encoding https://en.wikipedia.org/wiki/Base58
1. Bitcoin Base58 Alphabet https://en.bitcoinwiki.org/wiki/Base58#Alphabet_Base58
1. Linked Data Cryptographic Suite Registry https://w3c-ccg.github.io/ld-cryptosuite-registry
1. Verifiable Claims https://www.w3.org/TR/verifiable-claims-data-model
1. Asteroid SDK for Vivid https://github.com/Moonlight-io/asteroid-sdk-js
