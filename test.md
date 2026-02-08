# Test Tag for CBOR

This document specifies a Concise Binary Object Representation (CBOR) [1] tag for testing purposes.

    Tag: TBD (Proposed: 1413829460)
    Data item: Any
    Semantics: Explicitly none.
    Point of contact: Joe Hildebrand <joe-ietf@cursive.net>
    Description of semantics: https://github.com/hildjj/cbor-specs/blob/main/test.md

## Motivation

CBOR implementations should handle tags that they don't understand, usually by
creating an instance of an object that contains the tag number and the
associated data item.  In order to test this code, there needs to be a tag
number that will never be allocated for some new semantic.

## Semantics

This tag is always intended to represented in the same manner that an
unimplemented tag would be in a given implementation.

## Sample code

```js
import {encode, decode, Tag} from 'cbor2';
const t = new Tag(1413829460, 'TEST');
const bytes = encode(t); // 0xda544553546454455354
const t2 = decode(bytes); // 1413829460('TEST')
```

## Normative References

[1] C. Bormann, and P. Hoffman. "Concise Binary Object Representation (CBOR)". RFC 8949, December 2020. <https://www.rfc-editor.org/rfc/rfc8949.html>

## Author

Joe Hildebrand <mailto:joe-ietf@cursive.net>
