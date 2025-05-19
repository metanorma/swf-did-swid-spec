## Identifier

### Target System

The target system of the `did:swid` DID method is a registry known as Spatial
Web SWID Registry, which is managed by a Designated Authority.

### Method Name

The namestring that identifies this DID method is: `swid`. A DID that uses this
method MUST begin with the following prefix: `did:swid`. Per the DID
specification, this string MUST be in lowercase. The remainder of the DID, after
the prefix, is the [method-specific identifier](#method-specific-identifier),
specified below.

### Method-Specific Identifier

The `did:swid` method-specific identifier contains a [[ref:
self-certifying identifier]] (SCID) for the DID.
  
As specified in the following Augmented Backus-Naur Form (ABNF) notation
[[spec:rfc2234]] the [[ref: SCID]] **MUST** be present in the DID string. See
examples below. The `domain-segment` and `path-segment` elements refer to
[[spec:rfc3986]]'s ABNF for a Generic URL (page 49). Attempting to replicate
here the full ABNF of those elements from that RFC would inevitably be wrong.

```abnf
swid-did = "did:swid:" scid
scid = 46(base58-alphabet) ; The characters in the base58-btc-alphabet are as defined in the referenced W3C "Controller Documents" specification 
```

The `scid` part of the `did:swid` identifier is generated based on an initial
state of the SWID, as defined in the [SCID Generation and Verification](#scid-generation-and-verification)) section of
this specification.

Example SWID:

```
did:swid:QmfGEUAcMpzo25kF2Rhn8L5FAXysfGnkzjwdKoNPi615XQ
```
