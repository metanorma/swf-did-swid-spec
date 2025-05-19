## DID Method Processes

The [DID Method Operations](#did-method-operations) reference several processes
that are executed during [[ref: DIDDoc]] generation and DID resolution verification. Each
of those processes is specified in the following sections.

### `did:webvh` DID Method Parameters

Entries in the `did:webvh` [[ref: DID Log]] contain the JSON object `parameters`
that define the DID processing [[ref: parameters]] being used by the [[ref: DID
Controller]] when publishing the current and subsequent [[ref: DID log
entries]]. A DID Resolver **MUST** use the same [[ref: parameters]] when
processing the [[ref: DID Log]] to resolve the DID. The `parameters` object
**MUST** only include properties defined in this specification.

::: example

An example of the JSON prettified `parameters` property in the first [[ref: DID Log]] entry for a DID:

``` json
{
    "portable": true,
    "updateKeys": [
      "z82LkqR25TU88tztBEiFydNf4fUPn8oWBANckcmuqgonz9TAbK9a7WGQ5dm7jyqyRMpaRAe"
    ],
    "nextKeyHashes": [
      "enkkrohe5ccxyc7zghic6qux5inyzthg2tqka4b57kvtorysc3aa"
    ],
    "method": "did:webvh:1.0",
    "scid": "{SCID}"
}
```

:::

The allowed [[ref: parameter]] properties and (where applicable) enumerated values for those
properties are defined below.

- `method`: Defines the `did:webvh` specification version to use when processing a
  given DID's log file. As new versions of this specifications are defined,
  additional [[ref: parameters]] and enumerated values will be associated with
  the `method` values. This allows a long lasting DID to evolve the settings
  being used by the DID over time, such as changing the hash algorithms
  permitted, or allowing other [[ref: Data Integrity]] cryptosuites. See the [Cryptographic Agility section]() for more details.
  - This property **MUST** appear in the first [[ref: DID log entry]].
  - This property **MAY** appear in later [[ref: DID log entries]] to indicate that
    the processing rules for that and later [[ref: log entries]] have been changed to a
    different specification version.
  - Acceptable values for this specification are:
    - `did:webvh:1.0`: Requires that the rules defined in this version of the specification be used
      in processing the log. Implied by the value are the following:
      - The permitted hash algorithms used by the [[ref: DID Controller]] **MUST** be `SHA-256` as defined in [[spec:rfc6234]].
      - The permitted [[ref: Data Integrity]] cryptosuites used by the [[ref: DID Controller]] **MUST** be `eddsa-jcs-2022` as referenced in [[spec:di-eddsa-v1.0]].
- `scid`: The value of the [[ref: SCID]] for this DID.
  - This property **MUST** appear in the first [[ref: DID log entry]].
- `updateKeys`: An array of [[ref: multikey]] formatted public keys
  associated with the private keys that are authorized to sign the log entries
  that update the DID from one version to the next. An instance of the list in
  an entry replaces the previously active list. If an entry does not have the
  `updateKeys` property, the currently active list continues to apply. See the
  [Authorized Keys](#authorized-keys) section of this specification for
  additional details.
  - This property **MUST** appear in the first [[ref: DID log entry]] and **MAY**
    appear in subsequent entries, at the discretion of the
    [[ref: DID Controller]].
  - A key from the `updateKeys` array in the first [[ref: DID log entry]]
    **MUST** be used to authorize the initial [[ref: log entry]]. In all other
    [[ref: DID log entries]] without the [[ref: Key Pre-Rotation]] feature, an `updateKeys` property becomes active *after* the
    publication of its entry -- meaning its [[ref: log entry]] **MUST** be
    signed by a key the most recent `updateKeys` list from a **prior** [[ref:
    DID log]] entry. In [[ref: DID log entries]] with the [[ref: Key Pre-Rotation]] feature, an `updateKeys` property becomes 
    active *before* the publication of its entry -- meaning its [[ref: log entry]] **MUST** be
    signed by a key the `updateKeys` list from the **current** [[ref:
    DID log]] entry.
  - `updateKeys` **SHOULD** be set to `null` when deactivating the DID. See the
    [deactivate](#deactivate-revoke) section of this specification for more
    details.
  - `updateKeys` **MUST NOT** be set to an empty list `[]`.
- `portable`: A boolean (`true` / `false`) indicating if the DID is portable and
  thus can be renamed to change the Web location of the DID.
  - The value can **ONLY** be set to `true` in the first [[ref: log entry]], the initial version of the DID.
  - If not explicitly set in the first [[ref: log entry]], its value defaults to `false`.
  - If not explicitly set in other [[ref: DID log entries]], its value is retained from the prior [[ref: DID log entry]].
  - Once the value has been explicitly set to `false` in a [[ref: DID log entry]], it **MUST NOT** be set back to `true`.
  - See the section of this specification on [DID Portability](#did-portability)
    for more details about renaming a `did:webvh` DID.
- `nextKeyHashes`: An array of strings that are hashes of [[ref: multikey]]
  formatted public keys that **MAY** be added to the `updateKeys` list in the next
  [[ref: log entry]]. At least one entry of `nextKeyHashes` **MUST** be added to the next `updateKeys` list.
  - The process for generating the hashes and additional details for using [[ref: pre-rotation]] are defined in the
    [Pre-Rotation Key Hash Generation and Verification](#pre-rotation-key-hash-generation-and-verification)
    section of this specification.
  - If not explicitly set in the first [[ref: DID Log entry]], its value defaults to `null` and remains so in succeeding [[ref: DID log entries]] until explicitly set.
  - Once the [[ref: parameter]] `nextKeyHashes` has been set to a non-empty
    list, the [[ref: Key Pre-Rotation]] feature becomes active. While active the
    properties `nextKeyHashes` and `updateKeys` **MUST** be present in all
    [[ref: DID log entries]].
  - All [[ref: multikey]] formatted public keys added in a new `updateKeys` list **MUST** have their
    hashes listed in the `nextKeyHashes` list from the previous [[ref: DID log entry]].
  - A [[ref: DID Controller]] **MAY** put extra strings in the `nextKeyHashes`
    array that are not subsequently used in an `updateKeys` entry.
  - Any unused hashes in the prior `nextKeyHashes` are ignored.
  - The value of `nextKeyHashes` **MAY** be set to `null` to deactivate
    pre-rotation. For additional details about turning off [[ref: pre-rotation]]
    see the [pre-rotation](#pre-rotation-key-hash-generation-and-verification)
    section of this specification.
  - `nextKeyHashes` **MUST NOT** be set to an empty list `[]`.
- `witness`: A JSON object containing the [[ref: parameters]] for declaring the witnesses
  for the DID, and the [[ref: parameters]] for the process of updating a DID via a
  collaboration with [[ref: witnesses]] prior to publication. For details of
  this data and its usage in the approvals process, see the
  [DID Witnesses](#did-witnesses) section of this specification that includes the [witness parameter data structure  details](#the-witness-parameter).
  - A `witness` property in the first [[ref: DID log entry]] is immediately
    "active" and used to define the [[ref: witnesses]] and necessary [[ref: threshold]]
    for witnessing the initial [[ref: log entry]]. In all other [[ref: DID log entries]],
    a `witness` property becomes active **after** the publication of its entry
    -- meaning its [[ref: log entry]] **MUST** be witnessed by active
    `witnesses` from the most recent **prior** [[ref: DID log]] entry.
  - If the `witness` property is not set in the first [[ref: DID log entry]],
    its value defaults to `null`.
  - If the `witness` property is not set in [[ref: DID log entries]] after the first, its value is retained from the prior [[ref: DID log entry]].
  - The `witness` [[ref: parameter]] **MAY** be set to `null` to indicate that
    witnesses are not being used. If witnesses are active when the
    `witness` [[ref: parameter]] is set to `null`, that [[ref: DID log entry]]
    **MUST** be [[ref: witnessed]].
- `watchers`: An optional entry whose value is a JSON array containing a list of URLs ([[spec:rfc9110]]) that have notified the DID Controller that they are willing to watch the DID. See the [Watchers](#watchers) section of this specification for more details.
  - If the `watchers` property is not set in the first [[ref: DID log entry]], its value defaults to `null`.
  - If the `watchers` property is not set in [[ref: DID log entries]] after the first, its value is retained from the prior [[ref: DID log entry]].
- `deactivated`: A JSON boolean that **MUST** be initialized to `false` and
  **SHOULD** be set to `true` when the DID is to be deactivated but remains
  resolvable. See the [deactivate (revoke)](#deactivate-revoke) section of this
  specification for more details.
- `ttl`: A number, the number of seconds that a cache entry for a resolved
  `did:webvh` DID **SHOULD** last, as recommended by the [[ref: DID
  Controller]]. A resolver can use this value in deciding whether to retrieve a
  new version of the DID's `did.jsonl` file. If not specified, its value
  **MUST** be `null` and resolvers **MAY** set a default based on the business
  needs of the resolver clients.
  - Caching a `did:webvh` can be valuable in places where the business rules
    require resolving a number of DID URLs for the same DID. For example, a
    client might want call the resolver to the current [[ref: DIDDoc]], and then make
    repeated calls to get all of the previous versions of the [[ref: DIDDoc]]. By caching
    the [[ref: DIDDoc]] state, the resolver would not have to retrieve and process the
    [[ref: DID Log]] on each call.
  - A Web Server handling one or more `did.jsonl` files **MAY** be configured to
   use a comparable HTTP TTL per [[spec-inform:rfc9111]].

### Cryptographic Agility

The `did:webvh` DID method is designed to support cryptographic agility—the ability to adapt to evolving cryptographic algorithms and suites over time without breaking compatibility or requiring global coordination.

Cryptographic agility in `did:webvh` is achieved through the following mechanisms:

- **Self-describing cryptographic formats:** All cryptographically generated data (e.g., hashes and signatures) use formats that encode the algorithm used. Hashes use the [[ref: Multihash]] format, and signatures use the [[ref: Data Integrity]] Proofs. These formats allow verifiers to determine the algorithm from the data itself, enabling flexible and extensible support.

- **Specification versioning via the `method` parameter:** Each [[ref: DID Log Entry]] may include a `method` parameter that specifies the version of the `did:webvh` specification being used for the current and subsequent [[ref: log entries]]. This parameter is required in the initial log entry and may be updated in later entries to adopt newer versions of the specification. This allows long-lived DIDs to transition to updated algorithm sets over time.

- **Version-specific algorithm policies:** Each version of the `did:webvh` specification defines the permitted cryptographic algorithms and suites for that version. This constrains what algorithms [[ref: DID Controllers]] may use and limits the verification requirements placed on resolvers. For example, the v1.0 specification permits only one hash algorithm and one [[ref: Data Integrity]] cryptosuite, while future versions may change or expand this set.

- **Response to cryptographic vulnerabilities:** If flaws are identified in a cryptographic algorithm permitted by a given version of the specification, a new version of the `did:webvh` specification will be released that removes or replaces the compromised algorithms. DID Controllers may then rotate to the newer version by updating the `method` parameter in a new log entry.

This design allows `did:webvh` to remain interoperable and verifiable across cryptographic eras while minimizing the burden on resolvers and preserving backward compatibility where safe to do so.

### SCID Generation and Verification

The [[ref: self-certifying identifier]] or `SCID` is a required [[ref: parameter]] in the
first [[ref: DID log entry]] and is the hash of the DID's inception event.

#### Generate SCID

To generate the [[ref: SCID]] for a `did:webvh` DID, the DID Controller
**MUST** execute the following function:

 `base58btc(multihash(JCS(preliminary log entry with placeholders), <hash algorithm>))`

Where:

1. The `preliminary [[ref: log entry]] with placeholders` consists of the following
   pre-publication JSON object of what will become the first [[ref: log entry]]. The
   placeholder is the literal string "`{SCID}`".

   - The `versionId` entry, which **MUST** be `{SCID}`.
   - The `versionTime` entry, which **MUST** be a string that is the current time in
         UTC [[ref: ISO8601]] format, e.g., `"2024-04-05T07:32:58Z"`
   - The complete `parameters` for the initial [[ref: log entry]] as defined by the
     [[ref: DID Controller]], with the placeholder wherever the [[ref: SCID]] will
     eventually be placed.
   - The `state` JSON object with the value being the initial [[ref: DIDDoc]]
     with placeholders (the literal string "`{SCID}`") wherever the [[ref:
     SCID]] will eventually be placed in the [[ref: DIDDoc]].

2. `JCS` is an implementation of the [[ref: JSON Canonicalization Scheme]]
   [[spec:rfc8785]]. It outputs a canonicalized representation of its JSON
   input.
3. `multihash` is an implementation of the [[ref: multihash]] specification. Its
   output is a hash of the input using the associated `<hash algorithm>`,
   prefixed with a hash algorithm identifier and the hash size.
4. `<hash algorithm>` is the hash algorithm used by the [[ref: DID Controller]].
   The hash algorithm **MUST** be one listed in the
   [parameters](#didwebvh-did-method-parameters) defined by the version of the
   `did:webvh` specification being used by the [[ref: DID Controller]].
5. `base58btc` is an implementation of the [[ref: base58btc]] function.
   Its output is the base58 encoded string of its input.

#### Verify SCID

To verify the [[ref: SCID]] of a `did:webvh` DID being resolved, the resolver
**MUST** execute the following process:

1. Extract the first [[ref: DID log entry]] and use it for the rest of the steps
   in this process.
2. Extract the `scid` property value from the [[ref: parameters]] in the [[ref: DID log entry]].
3. Determine the hash algorithm used by the [[ref: DID Controller]] from the [[ref: multihash]] `scid` value.
   - The hash algorithm **MUST** be one listed in the
   [parameters](#didwebvh-did-method-parameters) defined by the version of the
   `did:webvh` specification being used by the [[ref: DID Controller]] based on the
   `method` [[ref: parameters]] property.
4. Remove the [[ref: data integrity]] proof property from the [[ref: DID log entry]].
5. Replace the `versionId` property value with the literal `"{SCID}"`.
6. Treat the resulting [[ref: log entry]] as a string and do a text replacement of the `scid`
   value from Step 2 with the literal string `{SCID}`.
7. Use the result and the hash algorithm (from Step 3) as input to the function
   defined in the [Generate SCID](#generate-scid) section (above).
8. The output string **MUST** match the `scid` extracted
   in Step 2. If not, terminate the resolution process with an error.

### Entry Hash Generation and Verification

The `entryHash` follows the version number and dash character `-` in the
`versionId` property in each DID log entry. Each `entryHash` is calculated
across its [[ref: log entry]], excluding the [[ref: Data Integrity]] proof. The
`versionId` used in the input to the hash is a predecessor value to the current
[[ref: log entry]], ensuring that the [[ref: entries]] are cryptographically "chained"
together in a microledger. For the first [[ref: log entry]], the predecessor
`versionId` is the SCID (itself a hash), while for all other entries it is the
`versionId` property from the previous log entry.

#### Generate Entry Hash

To generate the required hash for a `did:webvh` [[ref: log entry]], the [[ref: DID Controller]]
**MUST** execute the process `base58btc(multihash(JCS(entry), <hash algorithm>))` given a
preliminary [[ref: log entry]] as the string `entry`, where:

1. `JCS` is an implementation of the [[ref: JSON Canonicalization Scheme]]
   ([[spec:rfc8785]]). Its output is a canonicalized representation of its
   input.
2. `multihash` is an implementation of the [[ref: multihash]] specification. Its
   output is a hash of the input using the associated `<hash algorithm>`,
   prefixed with a hash algorithm identifier and the hash size.
3. `<hash algorithm>` is the hash algorithm used by the [[ref: DID Controller]].
   The hash algorithm **MUST** be one listed in the
   [parameters](#didwebvh-did-method-parameters) defined by the version of the
   `did:webvh` specification being used by the [[ref: DID Controller]].
4. `base58btc` is an implementation of the [[ref: base58btc]] function.
   Its output is the base58 encoded string of its input.

The following is an example of a preliminary [[ref: log entry]] that is processed to
produce an [[ref: entry hash]]. As this is a first entry in a [[ref: DID Log]], the input
`versionId` is the [[ref: SCID]] of the DID.

```json
{"versionId": "QmdmPkUdYzbr9txmx8gM2rsHPgr5L6m3gHjJGAf4vUFoGE", "versionTime": "2025-04-01T17:39:50Z", "parameters": {"witness": {"threshold": 2, "witnesses": [{"id": "did:key:z6Mkkc51mg2vpQzKWAbWQZupeGYhowaBjYkmvcKMTqteqHB4", "weight": 1}, {"id": "did:key:z6MkuDdJdKLCgwZuQuEi9xG6LVgJJ9Tebr74CXPYPSumqgJs", "weight": 1}, {"id": "did:key:z6MkoSWmQyp4fTk4ZQy4KUsss9dFX51XfEUzKKKj1J1JUsrF", "weight": 1}]}, "updateKeys": ["z6MkgzBDcBFV3sk4ypPE5YXMZHmS213A3HpYY2LmcVKV15jr"], "nextKeyHashes": ["QmZreDcjvWEpyRFznQeExWNCsvMLk5i59AcRJJuQC8UodJ"], "method": "did:webvh:0.5", "scid": "QmdmPkUdYzbr9txmx8gM2rsHPgr5L6m3gHjJGAf4vUFoGE"}, "state": {"@context": ["https://www.w3.org/ns/did/v1"], "id": "did:webvh:QmdmPkUdYzbr9txmx8gM2rsHPgr5L6m3gHjJGAf4vUFoGE:domain.example"}}

```

Resulting [[ref: entryHash]]: `QmQ6FJ4fk2xheSSQoEjVpTgx9AQPKhJgtR9hn1nr4EeCrZ`

#### Verify The Entry Hash

To verify the `entryHash` for a given  `did:webvh` [[ref: DID log entry]], a DID
Resolver **MUST** execute the following process:

1. Extract the `versionId` in the [[ref: DID log entry]], and
   remove from it the version number and dash prefix, leaving the log entry
   `entryHash` value.
2. Determine the hash algorithm used by the [[ref: DID Controller]] from the [[ref: multihash]] `entryHash` value.
   - The hash algorithm **MUST** be one listed in the
   [parameters](#didwebvh-did-method-parameters) defined by the version of the
   `did:webvh` specification being used by the [[ref: DID Controller]] based on the
   `method` [[ref: parameters]] property set in the current or most recent prior [[ref: log entry]].
3. Remove the [[ref: Data Integrity]] `proof` from the [[ref: log entry]].
4. Set the `versionId` in the entry object to be the `versionId` from the
   previous [[ref: log entry]]. If this is the first entry in the log, set the value to
   `<scid>`, the value of the [[ref: SCID]] of the DID.
5. Calculate the hash string as `base58btc(multihash(JCS(entry), <hash algorithm>))`, where:
   1. `entry` is the data from the previous step.
   2. `JCS` is an implementation of the [[ref: JSON Canonicalization Scheme]]
      ([[spec:rfc8785]]). Its output is a canonicalized representation of its
      input.
   3. `multihash` is an implementation of the [[ref: multihash]] specification.
      Its output is a hash of the input using the associated `<hash algorithm>`,
      prefixed with a hash algorithm identifier and the hash size.
   4. `<hash algorithm>` is the hash algorithm from Step 2.
   5. `base58btc` is an implementation of the [[ref: base58btc]] function.
      Its output is the base58 encoded string of its input.
6. Verify that the calculated value matches the extracted `entryHash` value from
   Step 1. If not, terminate the resolution process with an error.

### Authorized Keys

Each entry in the [[ref: DID Log]] **MUST** include a [[ref: Data Integrity]]
`proof` property signed by a key **authorized** to control (create, update, deactivate) the
DID, and with the `proofPurpose` set to `assertionMethod`. The authorized verification keys for `did:webvh` are the [[ref: multikey]]-formatted
public keys in the **active** `updateKeys` list from the `parameters` property of
the [[ref: log entries]]. Any of the authorized verification keys may be referenced
in the [[ref: Data Integrity]] proof.

For the first [[ref: log entry]] the **active** `updateKeys` list is the one in
that first [[ref: log entry]]. 

A resolver of the DID **MUST** verify the signature and the key used for signing
each [[ref: DID Log]] entry **MUST** be one from the list of active
`updateKeys`. If not, terminate the resolution process with an error.

The `did:webvh` Implementation Guide contains further discussion on the management
of keys authorized to update the DID.

The **active** `updateKeys` for subsequent [[ref: entries]] depends if the [[ref: Pre-Rotation]] is active or not.

#### No Key Prerotation

For all subsequent [[ref: entries]], the **active** list
is the most recent `updateKeys` **before** the [[ref: log entry]] to be verified. Thus,
the general case is that each [[ref: log entry]] is signed by the keys from the
**previous** [[ref: log entry]]. Once a [[ref: log entry]] containing an `updateKeys` list is
published, that `updateKeys` becomes the active list, and previous
`updateKeys` are ignored.

#### Prerotation

For all subsequent [[ref: entries]], the **active** list
is the `updateKeys` from the  **current** [[ref: log entry]] to be verified. Thus,
the general case is that each [[ref: log entry]] is signed by the keys from the
**current** [[ref: log entry]].

### DID Portability

As noted in the [Update (rotate)](#update-rotate) section of the specification,
a `did:webvh` DID can be renamed by changing the `id` DID string in the
DIDDoc to one that resolves to a different HTTPS URL if the following conditions are met.

- The [[ref: DID Log]] of the renamed DID **MUST** contain all of the [[ref: log entries]]
  from the creation of the DID.
- The [[ref: log entry]] in which the DID is renamed **MUST** be a valid DID entry
  building on the prior [[ref: DID log entries]], per this specification.
- The [[ref: parameter]] `portable` **MUST** be set to `true`, as defined in the
  [DID Method Parameters](#didwebvh-did-method-parameters) section.
- The [[ref: SCID]] **MUST** be the same in the original and renamed DID.
- The [[ref: DIDDoc]] **MUST** contain the prior DID string as an `alsoKnownAs` entry.
- [[ref: DID Controllers]] **SHOULD** account for any DNS requirements in making domain changes that impact a `did:webvh` DID being moved, such as those outlined in [[spec:1034]] (“Domain Names - Concepts and Facilities”), and [[spec:rfc1035]] (“Domain Names Implementation and Specification”).

### Pre-Rotation Key Hash Generation and Verification

Pre-rotation requires a [[ref: DID Controller]] to commit to the authorization
keys that will be used ("rotated to") in the next [[ref: log entry]] for updating the [[ref: DIDDoc]]. The purpose
of committing to future keys is that if the currently authorized keys are
compromised by an attacker, the attacker should not be able to take control of
the DID by using the compromised keys to rotate to new keys the attacker
controls. Assuming the attacker has not also compromised the committed key
pairs, they cannot rotate the authorization keys without detection. See the
non-normative section about [Using [[ref: Pre-Rotation]] Keys]([#using-pre-rotation-keys](https://didwebvh.info/latest/implementers-guide/prerotation-keys/))
in the `did:webvh` Implementer's Guide for additional guidance.

As described in the [parameters](#didwebvh-did-method-parameters) section of
this specification, a [[ref: DID Controller]] **MAY** include the [[ref: parameter]]
`nextKeyHashes` with a non-empty list in any [[ref: DID log entry]] to activate
the [[ref: pre-rotation]] feature. When [[ref: pre-rotation]] is active, all
[[ref: multikey]] representations of the public keys in the `updateKeys` [[ref:
parameters]] property in other than the initial version of the [[ref: DID log
entry]] **MUST** have their hash in the  `nextKeyHashes` array from the previous
[[ref: DID log entry]]. If not, terminate the resolution process with an error.

A [[ref: DID Controller]] may turn off the use of pre-rotation by setting the
[[ref: parameter]] `nextKeyHashes` to `null` in any [[ref: DID log entry]]. If
there is an active set of `nextKeyHashes` at the time, the pre-rotation
requirements remains in effect for the [[ref: DID Log entry]]. The subsequent
[[ref: DID Log entry]] **MUST** use the non-pre-rotation rules.

To create a hash to be included in the `nextKeyHashes` array, the [[ref: DID
Controller]] **MUST** execute the following process for each possible future
authorization key.

1. Generate a new key pair. The key type **MUST** be one that can be used as a
   `did:webvh` authorization key.
2. Generate a [[ref: multikey]] representation of the public key of the new key
   pair.
3. Calculate the hash string as `base58btc(multihash(multikey))`, where:
   1. `multikey` is the [[ref: multikey]] representation of the public key from Step 2.
   2. `multihash` is an implementation of the [[ref: multihash]] specification.
      Its output is a hash of the input using the associated `<hash algorithm>`,
      prefixed with a hash algorithm identifier and the hash size.
   3. `<hash algorithm>` is the hash algorithm used by the [[ref: DID Controller]].
      The hash algorithm **MUST** be one listed in the
      [parameters](#didwebvh-did-method-parameters) defined by the version of the
      `did:webvh` specification being used by the [[ref: DID Controller]].
   4. `base58btc` is an implementation of the [[ref: base58btc]] function.
      Its output is the base58 encoded string of its input.
4. Insert the calculated hash into the `nextKeyHashes` array being built up within
   the [[ref: parameters]] property.
5. The generated key pair **SHOULD** be safely stored so that it can be used in
   the next [[ref: log entry]] to become a DID authorization key. At that time, the
   [[ref: multikey]] representation of the public key will be inserted into the
   `updateKeys` property in the [[ref: parameters]] and the private key can be used to sign the [[ref: log entry]]'s DID update authorizations
   proofs.

A [[ref: DID Controller]] **MAY** add include extra entries (for keys or just random
strings) in a `nextKeyHashes` array.

When processing other than the first [[ref: DID log entry]] where
[[ref: pre-rotation]] feature is active, a `did:webvh` resolver **MUST**:

1. For each [[ref: multikey]] in the `updateKeys` property in the `parameters` of
   the [[ref: log entry]], calculate the hash and hash algorithm for the [[ref: multihash]]
   [[ref: multikey]].
2. The hash algorithm **MUST** be one listed in the
   [parameters](#didwebvh-did-method-parameters) defined by the version of the
   `did:webvh` specification being used by the [[ref: DID Controller]].
3. The resultant hash **MUST** be in the `nextKeyHashes` array from the previous [[ref: log entry]] prior to
   being processed. If not, terminate the resolution
   process with an error.
4. A new `nextKeyHashes` list **MUST** be in the `parameters` of the [[ref: log entry]]
   currently being processed. If not, terminate the resolution process with an error.
