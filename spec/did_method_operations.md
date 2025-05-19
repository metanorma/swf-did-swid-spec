## DID Method Operations

### Create (Register)

Creating a `did:swid` DID is done by carrying out the following steps.

1. **Define the DID string**
   The DID **MUST** be the literal string "`did:swid:{SCID}`", where the `{SCID}` is
   a placeholder that will be replaced by the calculated [[ref: SCID]] later in the process (see step
   5).

   The DID **MUST** be a valid `did:swid` DID as per the ABNF of a `did:swid` DID defined
   in the [Method-Specific  Identifier](#method-specific-identifier) section of this specification.

2. **Generate the authorization key pair(s)**
   [Authorized keys](#authorized-keys) are authorized to control (create, update, deactivate) the DID.
   At the same time, generate any other key pairs that will be placed into the initial
   [[ref: DIDDoc]] for the DID.

   1. If the DID is to use [[ref: pre-rotation]], additional key generation will
      be necessary to generate the required "next" authorization keys and their
      corresponding [[ref: pre-rotation]] hashes.
   2. For each authorization key pair, generate a [[ref: multikey]] based on the
      key pair's public key. The [[ref: multikey]] representations of the public
      keys are placed in the `updateKeys` property in [[ref: parameters]].
   3. The public key(s) of the authorization key pair(s) **MAY** be used in the
      [[ref: DIDDoc]] as well, but that is not required.

3. **Create the initial [[ref: DIDDoc]] for the DID**
   The [[ref: DIDDoc]] **MUST** contain the top level `id` property which **MUST** be the DID string from
   step 1, including the placement of the `{SICD}` placeholder for the [[ref: SCID]]. Other
   [[ref: DIDDoc]] verifications **SHOULD** be performed.

   All other absolute references to the DID in the [[ref: DIDDoc]] must use the form defined
   in step 1, with the identified placeholder for the [[ref: SCID]] (e.g., `did:swid:{SCID}#key-1`,
   `did:swid:{SCID}#key-1`, etc.).

   The [[ref: DIDDoc]] can contain any other content as deemed necessary by the [[ref: DID Controller]].

   1. Note: The placeholder (the string `{SCID}`) **MUST** be in every place in the [[ref: DIDDoc]] where
   the [[ref: SCID]] is to be placed.

4. **Generate a preliminary DID Log Entry** JSON object containing the same JSON
   properties that will be in the published [[ref: DID log entry]], but with some
   values preset, pending calculation of the [[ref: SCID]] and [[ref entryHash]] and
   without the `proof`.

   1. The value of `versionId` string **MUST** be the placeholder literal `"{SCID}"`.
   2. The value of `versionTime` string **MUST** be a valid UTC [[ref: ISO8601]] date/time string,
   and the represented time **MUST** be before or equal to the current time.
   3. The value of the `parameters` property **MUST** be a JSON object defined at the
      discretion of the [[ref: DID Controller]]. The properties in this nested JSON object
      **MUST** be as permitted in the [DID Generation and Verification
      Parameters](#didswid-did-method-parameters) section of this specification,
      and all required values in the first version of the DID **MUST** be
      present. In addition, where the [[ref: SCID]] of the DID is referenced in
      the parameters, the placeholder literal string `{SCID}` **MUST** be used
      in place of the to-be-calculated [[ref: SCID]].
   4. The value of the `state` property **MUST** be the initial [[ref: DIDDoc]] as
      defined in the previous step 3 of this process.

5. **Update the preliminary DID Log Entry to the initial DID Log Entry**
   Use the preliminary [[ref: DID log entry]] to perform the consecutive steps:

   1. **Calculate the [[ref: SCID]]**
   The preliminary JSON object **MUST** be used to calculate the [[ref: SCID]] for the DID as defined in
   the [SCID Generation and Verification](#scid-generation-and-verification) section of this
   specification.
   2. **Replace the placeholder `{SCID}`** Replace throughout the preliminary
   JSON object the placeholder "`{SCID}`" with the calculated [[ref: SCID]] from
   the previous step.
   3. **Calculate the [[ref: Entry Hash]]**
   The preliminary JSON object updated in the previous step **MUST** be used to calculate the [[ref: Entry Hash]]
   (`entryHash`) for the [[ref: log entry]], as defined in the
   [Entry Hash Generation and Verification](#entry-hash-generation-and-verification) section of
   this specification.
   4. **Replace the preliminary `versionId` value** The value of the `versionId`
   property **MUST** be updated with the literal string `1` (for version number 1),
   a literal `-`, followed by the `entryHash` value calculated in the previous
   step.
   5. **Generate the [[ref: Data Integrity]] proof** A [[ref: Data Integrity]]
   proof on the preliminary JSON object as updated in the previous step **MUST**
   be generated using an authorized key in the required `updateKeys` property in the
   [[ref: parameters]] object and the `proofPurpose` set to `assertionMethod`.

   6. **Add the [[ref: Data Integrity]] proof** The [[ref Data Integrity]] proof
   is added to the preliminary JSON object. The resultant JSON object is the
   initial [[ref: DID log entry]] for the DID.

6. **Generate the first [[ref: JSON Line]]**
   The [[ref: DID log entry]] **MUST** be updated to be a [[ref: JSON Lines]]
   entry by removing extraneous white space and appending a carriage return,
   and the result stored as the contents of the file `did.jsonl`.

7. **Publish the [[ref: DID Log]]**
   The complete [[ref: DID Log]] file **MUST** be published at the appropriate
   Web location defined by the `did:swid` DID identifier (see step 1)

     - This is a logical operation -- how a deployment serves the `did.jsonl`
       content is not constrained.
     - TODO: This happens using the SW registry

![swid-registration.png](./images/swid-registration.png)

### Read (Resolve)

The following steps MUST be executed to resolve the [[ref: DIDDoc]] for a `did:swid` DID:

   - TODO: Use the registry

1. The [DID-to-HTTPS Transformation](#the-did-to-https-transformation) steps
   **MUST** be used to transform the DID into an HTTPS URL for the [[ref: DID
   Log]] file.
2. Perform an HTTPS `GET` request to the URL using an agent that can successfully
   negotiate a secure HTTPS connection, which enforces the security requirements
   as described in
   [Security and privacy considerations](#security-and-privacy-considerations).
3. When performing the DNS resolution during the HTTPS GET request, the client
   SHOULD utilize [[spec:rfc8484]] in order to prevent tracking of the identity
   being resolved.
4. The [[ref: DID Log]] file **MUST** be processed as described below.

To process the retrieved [[ref: DID Log]] file, the resolver **MUST** carry out
the following steps on each of the [[ref: log entries]] in the order they appear in the
file, applying the [[ref: parameters]] set from the current and previous
entries. As noted in the [DID Log File](#the-did-log-file) section, [[ref: log entries]]
are each a JSON object with the following properties:

   1. `versionId`
   2. `versionTime`
   3. `parameters`
   4. `state` -- the version's [[ref: DIDDoc]].
   5. `proof` -- a [[ref: Data Integrity]] proof for the [[ref: log entry]].

For each entry:

1. Update the currently active [[ref: parameters]] with the [[ref: parameters]]
   from the entry (if any). The `parameters` **MUST** adhere to the [`did:swid`
   DID Method Parameters](#didswid-did-method-parameters) section of this
   specification. Continue processing using the now active set of [[ref:
   parameters]].
   - While all [[ref: parameters]] in the first [[ref: Log Entry]] take effect
     immediately, some kinds of [[ref: parameters]] defined in later [[ref:
     entries]] only take effect *after* that entry has been published. For
     example, updating the `nextKeys` and `witnesses` arrays take effect only
     *after* the entry in which they are defined has been published.
2. The [[ref: Data Integrity]] proof in the entry **MUST** be valid and signed by
   an authorized key as defined in the [Authorized Keys](#authorized-keys)
   section of this specification, and with a `proofPurpose` set to `assertionMethod`.
   1. If the [[ref: DID Controller]] has opted to use [[ref: witnesses]]
      resolvers **MUST** retrieve and verify the DID's `did-witness.json` file. For
      details, see the [DID Witnesses](#did-witnesses) section of this
      specification.
3. Verify the `versionId` for the entry. The `versionId` is the
   concatenation of the version number, a dash (`-`), and the `entryHash`.
   1. The version number **MUST** be `1` for the first [[ref: log entry]] and **MUST** be
      incremented by one for each subsequent [[ref: log entry]].
   2. A dash `-` **MUST** follow the version number.
   3. The `entryHash` **MUST** follow the dash, and **MUST** be verified using
      the process defined in the [Entry Hash Generation and
      Verification](#entry-hash-generation-and-verification) section of this
      specification.
4. The `versionTime` **MUST** be a valid UTC [[ref: ISO8601]] date/time string. The
   `versionTime` for each [[ref: log entry]] **MUST** be greater than the
   previous entry's time. The `versionTime` of the last entry **MUST** be
   earlier than the current time.
5. When processing the first [[ref: DID log]] entry, verify the [[ref: SCID]]
   (defined in the [[ref: parameters]]) according to the
   [SCID Generation and Verification](#scid-generation-and-verification) section
   of this specification.
6. Get the value of the [[ref: log entry]] property `state`, which is the [[ref:
   DIDDoc]] for the version.
      1. Track and check that the DID being resolved matches the top-level `id` in at least one version of the [[ref: DIDDoc]]; otherwise, the resolution **MUST** be flagged as invalid.
7. If [[ref: Key Pre-Rotation]] is being used, the hash of all `updateKeys` entries
   in the `parameters` property **MUST** match a hash in
   the array of `nextKeyHashes` [[ref: parameter]] from the previous [[ref: DID log]]
   entry, with exception of the first entry, as defined in the
   [Key [[ref: Pre-Rotation]] Hash Generation and Verification](#pre-rotation-key-hash-generation-and-verification)
   section of this specification.
8. As each [[ref: log entry]] is processed and verified, collect the following information
   about each version:
      1. The [[ref: DIDDoc]].
      2. The `versionId` of the [[ref: DIDDoc]].
      3. The UTC `versionTime`of the [[ref: DIDDoc]].
      4. The latest list of active [[ref: multikey]] formatted public keys
         authorized to update the DID, from the `updateKeys` lists in the
         [[ref: parameters]].
      5. If [[ref: pre-rotation]] is being used, the hashes of authorized keys that must
         be used in the `updateKeys` list of the next [[ref: DID log]] entry. The [[ref: pre-rotation]] hashes are in the
         `nextKeyHashes` list in the [[ref: parameters]].
      6. All other `did:swid` processing configuration settings as defined by in the
         `parameters` object.
      7. Add the value of top level `id` 
9. If the `parameters` for any of the versions define that some or all of
  the [[ref: DID Log entries]] must be witnessed, further verification of
  the [[ref: witness]] proofs must be carried out, as defined in the [DID
  Witnesses](#did-witnesses) section of this specification.

10.  Flag failed verifications appropriately, either invalidating the entire DID or marking all entries from the first invalid entry to the end of the log as invalid.

11.  Respond to the resolution request based on verification results:
      1. If all verifications pass, resolve the DID, applying any query parameters as requested.
      2. If the request includes query parameters (e.g., `?versionId=` or `?versionTime=`) that reference valid [[ref: DID log entries]], return the corresponding [[ref: DIDDoc]] version with a successful status code—even if later entries in the log are invalid.
      3. If the DID or DID version being resolved is invalid, return an appropriate error code.

While resolver caching policies are an implementation matter and largely outside the scope of this specification, resolvers **SHOULD NOT** cache a DID that fails verification. This ensures that the DID’s [[ref: DID Controller]] has the opportunity to recover a DID that may have been erroneously or maliciously invalidated.

A resolver **MAY** use a DID [[ref: watcher]] in addition to, or in place of retrieving the DID information from the source, and use that information based on their knowledge of the governance of the [[ref: watcher]]. See the [Watchers](#did-watchers) section of this specification for more details.

As defined in the [[spec:DID-RESOLUTION]] specification, a `did:swid` resolver should return the following DID Document Metadata when resolving a `did:swid` DID Document:

```json
{
  "versionId": "1-QmRRaLXwc6BjBuBPosSupJwEQ8w9f3znP7yfbpGfwcnLr6",
  "versionTime": "2025-01-23T04:12:36Z",
  "created": "2025-01-23T04:12:36Z",
  "updated": "2025-01-23T04:12:36Z",
  "scid": "QmPEQVM1JPTyrvEgBcDXwjK4TeyLGSX1PxjgyeAisdWM1p",
  "portable": false,
  "deactivated": false
}
```

where the items in the Metadata JSON object are:

- `versionId` — The `versionId` from the [[ref: Log Entry]] of the resolved DIDDoc version.
- `versionTime` — The `versionTime` from the [[ref: Log Entry]] of the resolved DIDDoc version, in [[ref: ISO8601]] timestamp format.
- `created` — The [[ref: ISO8601]] timestamp of the DID's first [[ref: log entry]], indicating when (according to the [[ref: DID Controller]]) the DID was created.
- `updated` — The [[ref: ISO8601]] timestamp of the DID's last valid [[ref: log entry]].
- `scid` — The [[ref: SCID]]] of the resolved DID.
- `portable` — A boolean value indicating whether the resolved DID has [[ref: portability]] active and so may be moved in the future, as defined in the [portability](#did-portability) section of this specification.
- `deactivated` — A boolean indicating whether the DID has been deactivated. When `true`, the DID is no longer active.

The "last valid [[ref: log entry]]" for some of the items above references the case where a DID resolution request references a DIDDoc that was valid, but where the DID Log has later [[ref: log entries]] that fail verification, as noted in the DID resolution steps earlier in this section. If all [[ref: log entries]] pass verification, the last valid [[ref: log entry]] is the last [[ref: log entry]].

When a DID resolution error occurs, the `error` field **MUST** be included in the `didResolutionMetadata`, as defined by the [[spec:DID-RESOLUTION]] specification. In addition, resolvers **SHOULD** provide supplemental "Problem Details" metadata following [[spec:rfc9457]], using the following structure:

```json
"didResolutionMetadata": {
  "error": "invalidDid",
  "problemDetails": {
    "type": "https://w3id.org/security#INVALID_CONTROLLED_IDENTIFIER_DOCUMENT_ID",
    "title": "The resolved DID is invalid.",
    "detail": "Parse error of the resolved DID at character 3, expected ':'."
  }
}
```

As described in [[spec:DID-EXTENSION-RESOLUTION]], the following values **MUST** be used in the `error` field of the resolution metadata when resolving a `did:swid` DID under the corresponding error conditions:

- `notFound` — The [[ref: DID Log]] or the resource referenced by a DID URL was not found. If the [[ref: DID Log]] does not exist at the DID's designated HTTPS location (according to the [DID-to-HTTPS Transformation](#the-did-to-https-transformation)), the resolver **MAY** attempt to retrieve it from alternative sources, such as [[ref: Watchers]], for verification and resolution.
- `invalidDid` — Any error that renders the `did:swid` DID invalid during resolution.

Resolvers **SHOULD** populate the `problemDetails` field to aid in diagnosing and understanding resolution failures.

### Update (Rotate)

To update a DID, a new, verifiable [[ref: DID Log Entry]] must be generated,
witnessed (if necessary), appended to the existing [[ref: DID Log]] (`did.jsonl`),
and published to the web location defined by the DID. The process to generate a
verifiable [[ref: DID Log Entry]] follows a similar process to the
[Create](#create-register) process, as follows:

1. Make the desired changes to the [[ref: DIDDoc]]. The top-level `id` in the
   [[ref: DIDDoc]] **MUST** contain the value of the DID.
   1. If the DID is configured to support [[ref: portability]], the root `id`
      property in the [[ref: DIDDoc]] **MAY** be changed when the [[ref: DID Controller]] wants to (or
      is forced to) publish the DID at a different Internet location and wants
      to retain the [[ref: SCID]] and history of the DID. For details, see the
      [DID Portability](#did-portability) section of this specification.
2. Define the [[ref: parameters]] JSON object to include the properties that affect the evolution of the
   DID. The `parameters` **MUST** be from those listed in the [`did:swid` DID
   Method Parameters](#didswid-did-method-parameters) section of this
   specification. Any [[ref: parameters]] defined in the JSON object override the
   previously active value, while any [[ref: parameters]] not included imply the
   existing values remain in effect. If no changes to the [[ref: parameters]]
   are needed, an empty JSON object `{}` **MUST** be used.
   - While all [[ref: parameters]] in the first [[ref: Log Entry]] take effect
     immediately, some types of [[ref: parameters]] defined in later [[ref: entries]] only take
     effect after the entry has been published. For example, rotating the keys
     authorized to update a DID or changing the [[ref: witnesses]] for a DID take effect
     only *after* the entry in which they are defined has been published.
3. Generate a preliminary [[ref: DID log entry]] JSON object containing the following properties:
   1. The value of `versionId` **MUST** be the value of `versionId` from the *previous* [[ref: DID log entry]].
   2. The `versionTime` value **MUST** be a string that is an [[ref: ISO8601]]
      format UTC timestamp. The time **MUST** be greater than the time of the
      previous [[ref: log entry]], and **MUST** be the time the DID will be
      retrieved by a [[ref: witness]] or resolver, or before.
   3. The [[ref: parameters]] passed in as a JSON object.
   4. Set the `state` JSON object to be the new version of the [[ref: DIDDoc]].
4. Calculate the new `versionId` of the new [[ref: DID Log Entry]], including
   incrementing the version number integer and using the process described in
   the [Entry Hash Generation and Verification](#entry-hash-generation-and-verification)
   section of this specification.
5. Replace the value of the `versionId` property in the preliminary [[ref: DID Log
   Entry]] with the value produced in the previous step.
6. Generate a [[ref: Data Integrity]] proof on the [[ref: DID log entry]] using
   an authorized key, as defined in the [Authorized Keys](#authorized-keys)
   section of this specification, and the `proofPurpose` set to `assertionMethod`.
7. If [[ref: Key Pre-Rotation]] is being used, the hash of all `updateKeys` entries
   in the `parameters` property **MUST** match a hash in
   the array of `nextKeyHashes` [[ref: parameter]] from the previous [[ref: DID log]] entry with exception of the first entry, as defined in the
   [Key [[ref: Pre-Rotation]] Hash Generation and Verification](#pre-rotation-key-hash-generation-and-verification)
   section of this specification.
8. The proof JSON object **MUST** be added as the value of the `proof` property in the [[ref: log entry]].
9. The entry **MUST** be made a [[ref JSON Line]] by removing extra whitespace, adding a `\n`
   to the entry. 
10. If the [[ref: DID Controller]] opts to use [[ref: witnesses]] for the
   DID, the [[ref: DID Controller]] **MUST** collect the [[ref: threshold]] of proofs
   from the DID's [[ref: witnesses]], and update and publish the DID's
   `did-witness.json` file. The updated `did-witness.json` file **MUST** be published
   **BEFORE** the updated [[ref: DID Log]] file is published. See the [DID
   Witnesses](#did-witnesses) section of this specification.
11. The new [[ref: log entry]] **MUST** be appended to the existing contents of
    the [[ref: DID Log]] file `did.jsonl`.
12. The updated [[ref: DID Log]] file **MUST** be published the appropriate
    location defined by the `did:swid` identifier.
    - This is a logical operation -- how a deployment serves the `did.jsonl`
    content is not constrained.
    - If there are [[ref: watchers]] configured for the DID, webhooks are triggered
   to notify the [[ref: watchers]] that an update is available and should be
   retrieved. See the [Watchers](#did-watchers) section of this specification for
   more details.

A controller **MAY** generate an equivalent, updated `did:web` [[ref: DIDDoc]] and
publish it as defined in the
[Publishing a Parallel `did:web` DID](#publishing-a-parallel-didweb-did)
section of this specification.

### Deactivate (Revoke)

To deactivate the DID, the [[ref: DID Controller]] **MUST** add to the [[ref:
DID log entry]] [[ref: parameters]] the property name and value `"deactivated":
true`. A [[ref: DID Controller]] **SHOULD** update the [[ref: DIDDoc]] and
`parameters` object to further indicate the deactivation of the DID, such as
setting to `null` the `updateKeys` in the [[ref: parameters]], preventing
further versions of the DID. If the DID is using [[ref: pre-rotation]], two
[[ref: DID log entries]] are required to accomplish that state, the first to
stop the use of pre-rotation, and the second for setting `updateKeys` to `null`.
For additional details about turning off [[ref: pre-rotation]] see the
[pre-rotation](#pre-rotation-key-hash-generation-and-verification) section of
this specification.

A resolver encountering in the [[ref: DID log entry]] [[ref: parameters]] the
property key:value pair `"deactivated": true` **MUST** return in the [[ref:
DIDDoc]] Metadata the property key:value `"deactivated": true`, as per the
[[spec:DID-RESOLUTION]] specification.

A [[ref: DID Controller]] can “deactivate” a DID by removing the published [[ref: DID Log]] and associated files and resources. Once removed, attempts to retrieve the [[ref: DID Log]] will result in an `HTTP 404 Not Found` (or an equivalent error status when resolving the DID). [[ref: Watchers]] monitoring a removed DID SHOULD continue to cache the last known valid state of the DID indefinitely so that their clients can still resolve and reference it, even after the [[ref: DID Log]] has been deleted.

