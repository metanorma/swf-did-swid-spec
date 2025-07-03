## DID Method Operations

### Create (Register)

#### Creating a `did:swid`

To create a `did:swid`, the Entity first generates a private/public key pair that controls the DID.
The public key is included as a verification method in the initial version of the DID document.

This initial version of the DID document MUST be a conformant SWID Document,
i.e. it MUST have an HSTP Service Endpoint.

See [[spec: SWIDS-AND-SWID-DOCUMENTS]] for more details about SWIDs and SWID Documents.

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

A `did:swid` or other type of SWID may be created by a "DID Issuer Service"
at behest of the Entity.

The following diagram describes the process of creating a SWID using a "DID Issuer Service":

![creating-a-swid-using-a-did-issuer-service.png](./images/creating-a-swid-using-a-did-issuer-service.png)

When creating a `did:swid` or other type of SWID, the "DID Issuer Service" MUST
enforce that the DID document associated with the SWID is a conformant SWID Document,
i.e. it MUST have an HSTP Service Endpoint. Otherwise, the "DID Issuer Service" MUST
return an error response.

See [[spec: SWIDS-AND-SWID-DOCUMENTS]] for more details about SWIDs and SWID Documents.

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

**Example Signing Request:**

```json
{
	"jobId": "96202012-41d8-424a-85a9-3673bda6abc7",
	"didState": {
		"state": "action",
		"action": "signPayload",
		"signingRequest": {
			"signingRequestCreate": {
				"kid": "#keys-1",
				"alg": "EdDSA",
				"purpose": "authentication",
				"serializedPayload": "<-- base 64 encoded -->"
			}
		}
	},
	"didRegistrationMetadata": { ... },
	"didDocumentMetadata": { ... }
}
```

**Example Signing Response:**

```json
{
    "jobId": "96202012-41d8-424a-85a9-3673bda6abc7",
    "options": {
        "clientSecretMode": true
    },
    "secret": {
        "signingResponse": {
            "signingRequestCreate": {
                "signature": "<-- base64 encoded -->"
            }
        }
    },
    "didDocument": {}
}
```

### Read (Resolve)

To resolve a `did:swid` or other type of SWID, the [`resolve` function](https://www.w3.org/TR/did-resolution/#resolving) of the
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

To update a `did:swid` or other type of SWID, the [`update` function](https://identity.foundation/did-registration/#update) of the
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

To deactivate a `did:swid` or other type of SWID, the [`deactivate` function](https://identity.foundation/did-registration/#deactivate) of the
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
