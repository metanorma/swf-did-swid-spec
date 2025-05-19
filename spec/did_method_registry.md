## The DID Method Registry

The SWID Registry contains a list of SWIDs. For each SWID, the SWID Registry contains a
cryptographic event log following the [Cryptographic Event Log](https://digitalbazaar.github.io/cel-spec/#the-did-document-cel-specification)
specification. Every entry in the event log is cryptographically signed by the entity
that controls a SWID.

For example, the SWID Registry could contain the following cryptographic event log for a
SWID that has been created, and then updated once:

```
{
  "log": [{
    "event": {
      "operation": {
        "type": "create",
        "data": {
          "@context": "https://www.w3.org/ns/did/v1.1",
          "id": "did:example:",
          "verificationMethod": [{
            "id": "#key-1",
            "type": "Multikey",
            "controller": "did:example",
            "publicKeyMultibase": "zDnaerx9CtbPJ1q36T5Ln5wYt3MQYeGRG5ehnPAmxcf5mDZpv"
          }],
          "authentication": ["#key-1"],
          "assertionMethod": ["#key-1"],
          "capabilityDelegation": [],
          "capabilityInvocation": []
          "proof": {
            "type": "DataIntegrityProof",
            "cryptosuite": "ecdsa-jcs-2019",
            "created": "2024-11-29T13:56:28Z",
            "verificationMethod": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#key-1",
            "proofPurpose": "assertionMethod",
            "proofValue": "z5obCSsrQxuFJdq6PrUMCtqY93gBHqGDBtQLPFxpZxzwVWgHYrXxoV"
          }
        }
      }
    }]
  }, {
    "event": {
      "previousEvent": "uEkoYyQ6YVtUmER8pN24wLZcLK9EBguM5WZlbAgfXBDuQiA"
      "operation": {
        "type": "update",
        "data": {
          "@context": "https://www.w3.org/ns/did/v1.1",
          "id": "did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
          "verificationMethod": [{
            "id": "did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#key-1",
            "type": "Multikey",
            "controller": "did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
            "publicKeyMultibase": "zDnaerx9CtbPJ1q36T5Ln5wYt3MQYeGRG5ehnPAmxcf5mDZpv"
          }, {
            "id": "did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#key-2",
            "type": "Multikey",
            "controller": "did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv",
            "publicKeyMultibase": "z6Mkf5rGMoatrSj1f4CyvuHBeXJELe9RPdzo2PKGNCKVtZxP"
          }],
          "authentication": ["did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#key-1"],
          "assertionMethod": ["did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#key-2"],
          "capabilityDelegation": ["did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#key-2"],
          "capabilityInvocation": ["did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#key-2"]
          "proof": {
            "type": "DataIntegrityProof",
            "cryptosuite": "ecdsa-jcs-2019",
            "created": "2024-11-30T17:03:42Z",
            "verificationMethod": "did:example:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#key-1",
            "proofPurpose": "assertionMethod",
            "proofValue": "z5obCSsrQxuFJdq6PrUMCtqY93gBHqGDBtQLPFxpZxzwVWgHYrXxoV"
          }
        }
      }
    }
  }]
}
```