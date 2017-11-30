# OpenSKOS 2 RESTful API

## TODO

- [ ] SPARQL paging experiments
  - use LIMIT and OFFSET
  - use a last seen concept
  - can we use the [hydra paging (`hydra:PartialCollectionView`)](http://www.hydra-cg.com/spec/latest/core/#collections) (envisioned part of the NDE generic API)
- [ ] how to handle URIs, e.g., register prefix in `application.ini`
  - user specified relation types
  - user provided properties
  - refering to OpenSKOS resources by their foreign URIs
- [ ] check if we follow RFC 6570 correctly
- [ ] what do we use for refering to internal resources
  - `uri` or `id` or both
  - e.g., `.../collections?institution=12345-6789-ABC` and/or `.../collections?institution=http://www.example.com/`
  - allow prefixes (see above)
- [ ] add cardinalities
- [ ] if an higher level includes concepts, e.g., for collections and concept schemes, also include the concept `<projection params>.props`?
  - the higher levels are equivalent to concept with `<filter params>`, remove them?
- [ ] camelCase in params, e.g., `conceptScheme`, but not in endpoints, e.g., `.../conceptscheme`?
- [ ] check how set and institution are expressed in OpenSKOS 2, e.g., `openskos:set` or `openskos:institution` (see concept `<projection params>`)?

## Foundation

* content negotiation
  * also a parameter (?format=) or uri part (.n3)
  * incl. HTML (on equal with JSON/RDF)
  * JSON-LD instead of proprietary JSON
* API key in header
  * good for security, bad for traceability (look at server logs and see who has done what)
  * focus on read-only first
* hide implementation details (Lucene query language)
* using just OpenSKOS generated URIs should work out of the box
* using foreign URIs (external SKOS imported into OpenSKOS) should work out of the box

## Notation

The API endpoints are described using [URI templates](http://www.rfc-editor.org/info/rfc6570). However, sets of parameters are pretty common, we name these groups and use `<parameter group name>` to refer to them in an URI.

## common variables

* {uri}=`the foreign URI of an resource` 
* {id}=`the native ID of an resource`
* {lang}=`known language code (BCP 47)`

## institutions

## sets

## concept schemes

`.../conceptscheme?<query params>&<filter params>&<limit params>`

`.../conceptscheme/{id}?<query params>` (native uri)

`.../conceptscheme/?uri={uri}&<query params>` (foreign uri)

`<query params>`
* level:
  * `1`: data properties of the concept scheme (default)
  * `2`: + object properties of the concept scheme
  * `3`: + data properties of the concept
  * `4`: + object properties of the concept

`<filter params>`
* institution=`institution`
* set=`set`

`<limit params>`
* limit=`nr of concept schemes to return`
* last=`offset or last seen concept scheme`

## collections

`.../collections?<query params>&<filter params>&<limit params>`

`.../collections/{id}?<query params>` (native uri)

`.../collections/?uri={uri}&<query params>` (foreign uri)

`<query params>`
* level:
  * `1`: data properties of the collection (default)
  * `2`: + object properties of the collection
  * `3`: + data properties of the concept
  * `4`: + object properties of the concept

`<filter params>`
* institution=`institution`
* set=`set`

`<limit params>`
* limit=`nr of collections to return`
* last=`offset or last seen collection`

## concepts

`.../concepts?<selection params>&<filter params>&<projection params>&<order params>&<limit params>`

`.../concepts/{id}?<projection params>` (native uri)

`.../concepts?uri={uri}<projection params>` (foreign uri)

`<selection params>`
* text=`search terms`
  * support for
    * strings: ab c
    * wildcard: \*a a\* a*b
    * boolean: a AND b OR NOT c
    * brackets: (a OR b) AND c
    * phrases: "ab c"
    * escapes: \\" \\* \\\\
* fields=`comma separated list of`
  * default: label
  * label(@{lang})?
    * prefLabel(@{lang})?
    * altLabel(@{lang})?
    * hiddenLabel(@{lang})?
  * note(@{lang})?
    * definition(@{lang})?
    * example(@{lang})?
    * changeNote(@{lang})?
    * historyNote(@{lang})?
    * scopeNote(@{lang})?
    * editorialNote(@{lang})?
  * notation

`<filter params>`
* institution=`institution`
* set=`set`
* conceptScheme=`concept scheme`
* collection=`collection`

`<projection params>`
* level=`which properties to return`
  * `1`: data properties of the concepts (default)
  * `2`: + object properties of the concepts
* props=`comma separated list of`
  * default: uri,prefLabel,definition
  * uri
  * label(@{lang})?
    * prefLabel(@{lang})?
    * altLabel(@{lang})?
    * hiddenLabel(@{lang})?
  * note(@{lang})?
    * definition(@{lang})?
    * example(@{lang})?
    * changeNote(@{lang})?
    * historyNote(@{lang})?
    * scopeNote(@{lang})?
    * editorialNote(@{lang})?
  * notation
  * semanticRelation
    * broader
    * narrower
    * related
    * broaderTransative
    * narrowerTransative
    * mappingRelation
      * closeMatch
      * exactMatch
      * broadMatch
      * narrowMatch
      * relatedMatch
  * inScheme
    * topConceptOf
  * openskos:inCollection (OpenSKOS specific inverse of skos:member) [NOTE: currently inSkosCollection]
  * openskos:status
  * openskos:set [TODO: check]
  * openskos:institution [TODO: check]
  * {user relation types} [TODO: how to handle URIs, e.g., register prefix in application.ini]
  * {user properties}
* lang=`comma separated list of known language codes (BCP 47)`
  * if not specified all languages are projected (default)

`<order params>`
* order=`comma separated list of`
  * prefLabel(@{lang})?(:(asc|desc))? (default direction: asc)
  * altLabel(@{lang})?(:(asc|desc))? (default direction: asc)
  * hiddenLabel(@{lang})?(:(asc|desc))? (default direction: asc)
  * notation(:(asc|desc))? (default direction: asc)
  * uri(:(asc|desc))? (??) (default direction: asc)
  * score(:(asc|desc))? (default) (default direction: desc)
* institution
* set
* conceptScheme
* collection

`<limit params>`
* limit=`nr of concepts to return`
* last=`last seen concept`

## autocomplete

`.../autocomplete?<selection params>&<projection params>&<filter params>`

`<selection params>`
* text=`substring to match case insensitively`
* fields=`comma separated list of`
  * default: label
  * label(@{lang})?
    * prefLabel(@{lang})?
    * altLabel(@{lang})?
    * hiddenLabel(@{lang})?
  * notation

`<projection params>`
* props=`comma separated list of`
  * default: uri,prefLabel
  * uri
  * label(@{lang})?
    * prefLabel(@{lang})?
    * altLabel(@{lang})?
    * hiddenLabel(@{lang})?
  * notation

`<filter params>`
* institution=`institution`
* set=`set`
* conceptScheme=`concept scheme`
* collection=`collection`

## relations

`.../relations?<selection params>&<projection params>&<limit params>`

`<selection params>`
* subject=`uri`
* types=`comma separated list of relation types`
  * semanticRelation
    * broader
    * narrower
    * related
    * broaderTransative
    * narrowerTransative
    * mappingRelation
      * closeMatch
      * exactMatch
      * broadMatch
      * narrowMatch
      * relatedMatch
  * inScheme
    * topConceptOf
  * openskos:inCollection (OpenSKOS specific inverse of skos:member) [NOTE: currently inSkosCollection]
  * member
  * hasTopConcept
  * {user relation types} [TODO: how to handle URIs, e.g., register prefix in application.ini]
* object=`uri`

`<projection params>`
* props=`comma separated list of`
  * default: uri
  * uri
  * title(@{lang})?
  * label(@{lang})?
    * prefLabel(@{lang})?
    * altLabel(@{lang})?
    * hiddenLabel(@{lang})?

`<limit params>`
* limit=`nr of subjects to return`
* last=`last seen subject`

## relation types (??)

## user (??)

## status (??)