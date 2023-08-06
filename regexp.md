# ECMAScript Regular Expressions for CBOR

This document specifies a tag for a ECMAScript [1] RegExp (Regular Expression) in Concise Binary Object Representation (CBOR) [2].

    Tag: 21066
    Data item: Array[UTF8string, UTF8string?]
    Semantics: ECMAScript RegExp https://262.ecma-international.org/14.0/#sec-regexp-regular-expression-objects
    Point of contact: Joe Hildebrand <joe-ietf@cursive.net>
    Description of semantics: https://github.com/hildjj/cbor-specs/blob/main/regexp.md

## Motivation

It would be nice to be able to round-trip all ECMAScript built-in types that
consist of pure data (no methods) to and from CBOR.

For example, there exists at least one testing framework that uses CBOR to
serialize snapshots of expected results that need to be serialized back to
ECMAScript.

## Semantics

Tag 21066 is applied to an array containing one or two strings.  The first string
is always the regular expression `pattern`, which can be obtained from an
ECMAScript RegExp object with the `source` property.  The first string should
not be empty (`""`).  To signal the empty regular expression, ECMAScript-262
recommends `"(?:)"`.  Note however, that an empty string does not cause
problems in practice, since the RegExp constructor will accept an empty string
(whereas the slash syntax "/foo/gu" does not).  If the first element of the
array is any type other than UTF8String, the RegExp constructor will first
apply String() to its first argument, with sometimes surprising, but not
catastrophic results.

If the second string is specified in the array, it represents zero or more
single-letter flags that modify the function of the regular expression.  The
flags can be obtained from an ECMAScript RegExp object with the `flags`
property.  Note that in ECMAScript RegExps, the order of flags is not
significant.  The second string may be absent, which is equivalent to
`undefined`.  It may also be an empty string (`""`).  If the second element of
the array is anything other than not present, undefined, or a string
consisting of currently-valid flags, the RegExp constructor will correctly
throw an exception.

When decoding, if the first element of the array is not a string, signal an
error.  If the first element is the empty string, it is safe to pass into the
RegExp constructor.  The first element of the array MUST be passed as the
first parameter of the RegExp constructor, named `pattern`.  If the second
string is specified, it MUST be used as the second parameter to the RegExp
constructor, named `flags`.

Subsequent versions of ECMA-262 may implement new RegExp features or new
RegExp flags.  Receivers MAY signal an error when receiving these newer
features or flags.  This same interoperability challenge applies to all web
content, however, so it is not deemed important enough to warrant version or
capability signaling in the protocol.

## Sample code

```js
// Encoding:
const r = /foo/gu;
const tag = new Tag(21066, [r.source, r.flags]);

// Decoding:
// (Note: tag.value is an array of 1 or 2 items.  If only one, the constructor
// will get `undefined` for the flags using this approach, which is fine.)
if (tag.value.length < 1 || tag.value.length > 2) {
    throw new RangeError('Invalid array size for tag 21066 converting to RegExp');
}
const r = new RegExp(...tag.value);
```

## History

A less-specified version of this tag was published in RFC 7049 [3], section 2.4.4.3.  RFC 8949 notes:

    [RFC7049] also defined a tag number 35 for regular expressions that are in
    Perl Compatible Regular Expressions (PCRE/PCRE2) form [PCRE] or in JavaScript
    regular expression syntax [ECMA262]. The state of the art in these regular
    expression specifications has since advanced and is continually advancing, so
    this specification does not attempt to update the references. Instead, this
    tag remains available (as registered in [RFC7049]) for applications that
    specify the particular regular expression variant they use out-of-band
    (possibly by limiting the usage to a defined common subset of both PCRE and
    ECMA262). As this specification clarifies tag validity beyond [RFC7049], we
    note that due to the open way the tag was defined in [RFC7049], any contained
    string value needs to be valid at the CBOR tag level (but then may not be
    "expected" at the application level).

This document greatly restricts the form of regular expressions that are
supported, and makes clear how to handle the flags that alter regular
expression usage in relatively important ways.

## Normative References

[1] [ECMA-262] Ecma International, "ECMAScript® 2023 Language Specification", Standard ECMA-262, <https://262.ecma-international.org/>.

[2] C. Bormann, and P. Hoffman. "Concise Binary Object Representation (CBOR)". RFC 8949, December 2020. <https://www.rfc-editor.org/rfc/rfc8949.html>

## Informative References

[3] C. Bormann, and P. Hoffman. "Concise Binary Object Representation (CBOR)". RFC 7049, October 2013. https://www.rfc-editor.org/rfc/rfc7049.html

## Author

Joe Hildebrand <mailto:joe-ietf@cursive.net>
