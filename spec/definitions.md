## Definitions

[[def: self-certifying identifier, self-certifying identifiers, SCID, SCIDs]]

~ An object identifier derived from initial data such that an attacker could not
create a new object with the same identifier.

[[def: Data Integrity]]

~ [W3C Data
Integrity](https://www.w3.org/community/reports/credentials/CG-FINAL-data-integrity-20220722/)
is a specification of mechanisms for ensuring the authenticity and integrity of
structured digital documents using cryptography, such as digital signatures and
other digital mathematical proofs.

[[def: Decentralized Identifier, Decentralized Identifiers, DID, DIDs]]

~ Decentralized Identifiers (DIDs) [[spec:did-core]] are a type of identifier that enable
verifiable, decentralized digital identities. A DID refers to any subject (e.g.,
a person, organization, thing, data model, abstract entity, etc.) as determined
by the controller of the DID.

[[def: DID Controller, DID Controller's, DID Controllers]]

~ The entity that controls (create, updates, deletes) a given DID, as defined
in the [[spec:DID-CORE]].

[[def: DIDDoc]]

~ A DID Document as defined by the [[spec: DID-Core]] -- the document returned when a DID is resolved.

[[def: DID Method, DID Methods]]

~ DID methods are the mechanism by which a particular type of DID and its
associated DID document are created, resolved, updated, and deactivated. DID
methods are defined using separate DID method specifications.

[[def: ISO8601, ISO8601 String]]

~ A date/time expressed using the [ISO8601
Standard](https://en.wikipedia.org/wiki/ISO_8601).

[[def: JSON Canonicalization Scheme]]

~ [[spec:rfc8785]] defines a method for canonicalizing a JSON
structure such that is suitable for verifiable hashing or signing.

[[def: multibase]]

~ A specification for encoding binary data as a string using a prefix that
indicates the encoding.

[[def: multikey]]

~ A verification method that encodes key types into a single binary stream that
is then encoded as a [[ref: multibase]] value.

[[def: multihash]]

~ Per the [[spec:multiformats]], [[ref: multihash]] is a specification
for differentiating instances of hashes. Software creating a hash prefixes
(according to the specification) data to the hash indicating the algorithm used
and the length of the hash, so that software receiving the hash knows how to
verify it. Although [[ref: multihash]] supports many hash algorithms, for
interoperability, [[ref: DID Controllers]] **MUST** only use the hash algorithms defined
in this specification as permitted.

[[def: W3C VCDM]]

~ A Verifiable Credential that uses the Data Model defined by the W3C [[spec: W3C-VC]] specification.
