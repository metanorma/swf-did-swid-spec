## DID Method Operations

### Create (Register)

The following diagram describes the process of creating and registering a `did:swid` (SWID):

![swid-registration.png](./images/swid-registration.png)

#### Creating a SWID

To create a SWID, the Entity first generates a private/public key pair that controls the DID.
The public key is included as a verification method in the initial version of the DID document.
The initial version of the DID document MAY also contain other information such as an
[HSTP service endpoint](#hstp-service-endpoint-type).

**Example initial DID document:**

```json
{
   "@context": [
      "https://www.w3.org/ns/did/v1.1",
      "https://spatialwebfoundation.org/contexts/did/1.0"
   ],
   "verificationMethod": [{
      "id": "#keys-1",
      "type": "Multikey",
      "publicKeyMultibase": "z6MkmM42vxfqZQsv4ehtTjFFxQ4sQKS2w6WR7emozFAn5cxu"
   }],
   "authentication": [
     "#keys-1"
   ],
   "service": [{
      "id": "#hstp",
      "type": "HSTPEndpoint",
      "serviceEndpoint": "https://hstp.example.com/hstpendpoint"
   }]
}
```

Using the initial version of the DID document, the first entry of a [Cryptographic Event Log](https://digitalbazaar.github.io/cel-spec/#the-did-document-cel-specification)
is constructed (also see [The SWID Registry](#the-swid-registry)).
A cryptographic digest (hash) of this entry is created and encoded using [[ref: multihash]], and
is used as the `scid` part of the `did:swid` identifier (see [Identifier](#identifier)).

**Example SWID:**

```
did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv
```

#### Using a DID Issuer Service

A SWID may be created by a "DID Issuer Service" at behest of the Entity.

To create a SWID using a "DID Issuer Service", the [`create` function](https://identity.foundation/did-registration/#create) of the
DIF [[spec: DID-REGISTRATION]] specification is used.

**Example Request to create a SWID:**

```
HTTP POST to https://<did-issuer>/create?method=swid
```

```json
{
  "options": {
    "clientSecretMode": true
  },
  "secret": { },
  "didDocument": {
     "@context": [
        "https://www.w3.org/ns/did/v1.1",
        "https://spatialwebfoundation.org/contexts/did/1.0"
     ],
     "verificationMethod": [{
        "id": "#keys-1",
        "type": "Multikey",
        "publicKeyMultibase": "z6MkmM42vxfqZQsv4ehtTjFFxQ4sQKS2w6WR7emozFAn5cxu"
     }],
     "authentication": [
       "#keys-1"
     ],
     "service": [{
        "id": "#hstp",
        "type": "HSTPEndpoint",
        "serviceEndpoint": "https://hstp.example.com/hstpendpoint"
     }]
  }
}
```

**Example Response:**

```json
{
  "didState": {
    "state": "finished",
    "did": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
    "secret": { }
  },
  "didRegistrationMetadata": {  },
  "didDocumentMetadata": {  }
}
```

During the process of creating a SWID, the Entity MAY be required to prove control
of its SWID's associated public key, using  [Signing Requests](https://identity.foundation/did-registration/#signing-request-set)
and [Signing Responses](https://identity.foundation/did-registration/#signing-response-set).

#### Registering a SWID in a Registry

Registering a SWID in a registry is an optional step after creating the SWID. Most SWIDs will not be recorded
in a centralized registry. SWIDs may be registered in a system of distributed, decentralized registries.

The process, requirements, and interfaces for registering a SWID in a registry is currently out-of-scope
of this specification.

### Read (Resolve)

To resolve a SWID, the [`resolve` function](https://www.w3.org/TR/did-resolution/#resolving) of the
W3C [[spec: DID-RESOLUTION]] specification is used.

**Example Request to resolve a SWID:**

```
HTTP POST to https://<swid-resolver>/identifiers/did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv
```

**Example Response:**

```json
{
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1.1",
      "https://spatialwebfoundation.org/contexts/did/1.0"
    ],
    "id": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
    "verificationMethod": [{
      "id": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#keys-1",
      "type": "Multikey",
      "controller": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
      "publicKeyMultibase": "z6MkmM42vxfqZQsv4ehtTjFFxQ4sQKS2w6WR7emozFAn5cxu"
    }],
    "authentication": [
      "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#keys-1"
    ],
    "service": [{
      "id": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#hstp",
      "type": "HSTPEndpoint",
      "serviceEndpoint": "https://hstp.example.com/hstpendpoint"
    }]
  },
  "didResolutionMetadata": { },
  "didDocumentMetadata": { }
}
```

### Update (Rotate)

To update a SWID, the [`update` function](https://identity.foundation/did-registration/#update) of the
DIF [[spec: DID-REGISTRATION]] specification is used.

Updating a SWID can include the following:
- Rotating the public key that controls the SWID.
- Changing the HSTP service endpoint of the SWID.

**Example Request to update a SWID:**

```
HTTP POST to https://<swid-registry>/update
```

```json
{
  "did": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
  "options": {
    "clientSecretMode": true
  },
  "secret": { },
   "didDocument": {
      "@context": [
         "https://www.w3.org/ns/did/v1.1",
         "https://spatialwebfoundation.org/contexts/did/1.0"
      ],
      "id": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
      "verificationMethod": [{
         "id": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#keys-1",
         "type": "Multikey",
         "controller": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
         "publicKeyMultibase": "z6MkmM42vxfqZQsv4ehtTjFFxQ4sQKS2w6WR7emozFAn5cxu"
      }],
      "authentication": [
         "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#keys-1"
      ],
      "service": [{
         "id": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#hstp",
         "type": "HSTPEndpoint",
         "serviceEndpoint": "https://hstp.example.com/new_hstpendpoint"
      }]
   }
}
```

**Example Response:**

```json
{
  "didState": {
    "state": "finished",
    "did": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv", 
    "secret": { }
  },
  "didRegistrationMetadata": {  },
  "didDocumentMetadata": {  }
}
```

During the process of updating a SWID, the Entity MAY be required to prove control
of its SWID's associated public key, using  [Signing Requests](https://identity.foundation/did-registration/#signing-request-set)
and [Signing Responses](https://identity.foundation/did-registration/#signing-response-set).

### Deactivate (Revoke)

To update a SWID, the [`deactivate` function](https://identity.foundation/did-registration/#deactivate) of the
DIF [[spec: DID-REGISTRATION]] specification is used.

**Example Request to deactivate a SWID:**

```
HTTP POST to https://<swid-registry>/deactivate
```

```json
{
  "did": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
  "options": {
    "clientSecretMode": true
  },
  "secret": { }
}
```

**Example Response:**

```json
{
  "didState": {
    "state": "finished",
    "did": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv", 
    "secret": { }
  },
  "didRegistrationMetadata": {  },
  "didDocumentMetadata": {  }
}
```

During the process of deactivating a SWID, the Entity MAY be required to prove control
of its SWID's associated public key, using  [Signing Requests](https://identity.foundation/did-registration/#signing-request-set)
and [Signing Responses](https://identity.foundation/did-registration/#signing-response-set).
