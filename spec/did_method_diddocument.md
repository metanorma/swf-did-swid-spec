## DID Document

The `did:swid` method supports all features of DID documents defined by the [[spec: DID-CORE]]
specification, including verification methods and service endpoints.

### HSTP Service Endpoint Type

This specification defines a new service endpoint type `HSTPEndpoint`, which points to a
Hyperspace Transaction Protocol (HSTP) endpoint for a Spatial Web Node where the entity
lives.

```json
{
  "service": [{
    "id": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#hstp",
    "type": "HSTPEndpoint",
    "serviceEndpoint": "https://hstp.example.com/hstpendpoint"
  }]
}
```

### Example DID Document

The following is an example of a complete DID document for a `did:swid` DID:

```json
{
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
  "assertionMethod": [
    "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#keys-1"
  ],
  "service": [{
    "id": "did:swid:zQmQoeG7u6XBtdXoek5p3aPoTjaSRemHAKrMcY2Hcjpe3jv#hstp",
    "type": "HSTPEndpoint",
    "serviceEndpoint": "https://hstp.example.com/hstpendpoint"
  }]
}
```
