## DID Method Operations

### Create (Register)

The following diagram describes the process of creating a `did:swid`:

![swid-registration.png](./images/swid-registration.png)

To create a SWID, the [`create` function](https://identity.foundation/did-registration/#create) of the
DIF [[spec: DID-REGISTRATION]] specification is used.

Before creating a SWID, the Entity first generated a private/public key pair that controls the DID.
The public key is included in the initial version of the DID document.

Example Request to create a SWID:

```
HTTP POST to https://<did-issuer>/create?method=swid"
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

Example Response:

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

### Read (Resolve)

To resolve a SWID, the [`resolve` function](https://www.w3.org/TR/did-resolution/#resolving) of the
W3C [[spec: DID-RESOLUTION]] specification is used.

Example Request to resolve a SWID:

```
HTTP POST to https://<swid-resolve>/identifiers/did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv"
```

Example Response:

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

Example Request to update a SWID:

```
HTTP POST to https://<swid-registry>/update"
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

Example Response:

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

### Deactivate (Revoke)

To update a SWID, the [`deactivate` function](https://identity.foundation/did-registration/#deactivate) of the
DIF [[spec: DID-REGISTRATION]] specification is used.

Example Request to deactivate a SWID:

```
HTTP POST to https://<swid-registry>/deactivate"
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

Example Response:

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
