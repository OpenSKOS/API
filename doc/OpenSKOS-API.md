# OpenSKOS 2 RESTful API

## TODO

- [ ] SPARQL paging experiments
  - [ ] use LIMIT and OFFSET
  - [ ] use a last seen concept
  - [ ] can we use the [hydra paging (`hydra:PartialCollectionView`)](http://www.hydra-cg.com/spec/latest/core/#collections)? (envisioned part of the NDE generic API)
- [ ] how to handle URIs
  - e.g., register prefix in `application.ini` (see [prefixes](#prefixes)
  - user specified relation types
  - user provided properties
  - refering to OpenSKOS resources by their foreign URIs
- [ ] check if we follow RFC 6570 correctly
- [X] what do we use for refering to internal resources
  - [X] `uri` or `id` or both
  - [ ] allow prefixes (see above)
- [ ] add cardinalities
- [X] if an higher level includes concepts, e.g., for collections and concept schemes, also include the concept `<projection params>.props`? NO
- [X] camelCase in params, e.g., `conceptScheme`, but not in endpoints, e.g., `.../conceptscheme`? OK as it is
- [ ] check how set and institution are expressed in OpenSKOS 2, e.g., `openskos:set` or `openskos:institution` (see concept `<projection params>`)?
- [ ] analyze if major (read) functionality of the editor is covered in an appropriate amount of requests, e.g., a list should not require a call for each concept
- [ ] error codes
  - 400 if other parameters have a clash with search profile
- [ ] return representations (at least JSON-LD, RDF/XML, TriG/TriX, incl. Hydra paging vocab)
- [X] support for dates in projection, selection/filter and order
  - submitted
  - modified
  - accepted
  - openskos:deleted
  - NOTE: SPARQL has no support for xsd:duration, but does have support for operators on xsd:dateTime, so some (all?) ranges can be translated into boolean queries using operators (`2018` => `x >= xsd:dateTime('1-1-2018') and x <= xsd:dateTime('31-12-2018')`)
  - NOTE: maybe support some shortcuts, e.g., today, yesterday, this week, last week, this month, last month, this year, last year
- [X] support for status in projection, selection/filter and order (?)
- [X] support for user in projection, selection/filter and order (?)
  - creator
  - openskos:modifiedBy
  - openskos:acceptedBy
  - openskos:deletedBy
- [X] support for search profiles
- [ ] concepts is used as the main discussion endpoint, sync with other endpoints
- [X] support for SKOS-XL labels
- [ ] check consistent use of institution vs tenant
- [ ] discuss the first class citizenship of `skosxl:Labels`

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
* using foreign URIs (external SKOS imported into OpenSKOS) should work out of the box (just don't use id endpoints)

## Notation

The API endpoints are described using [URI templates](http://www.rfc-editor.org/info/rfc6570). However, sets of parameters are pretty common, we name these groups and use `<parameter group name>` to refer to them in an URI.

## common variables

* {uri}=`the foreign URI of an resource` 
* {id}=`the native ID of an resource`
* {lang}=`known language code (BCP 47)`

## institutions

## sets

## concept schemes

`.../conceptschemes?<query params>&<filter params>&<limit params>`

`.../conceptscheme/{id}?<query params>` (native uri)

`.../conceptscheme?uri={uri}&<query params>` (foreign uri)

`<query params>`
* level:
  * `1`: data properties of the concept scheme (default)
  * `2`: + object properties of the concept scheme
  * `3`: + data properties of the concept
  * `4`: + object properties of the concept

NOTE: the OpenSKOS editor should never use level `3` or `4`, they more interesting for external LD parties

`<filter params>`
* institutions=`comma separated list of institution URIs or IDs`
* sets=`set`
* searchProfile=`id of a search profile`

`<limit params>`
* limit=`nr of concept schemes to return`
* last=`offset or last seen concept scheme`

## collections

`.../collections?<query params>&<filter params>&<limit params>`

`.../collection/{id}?<query params>` (native uri)

`.../collection?uri={uri}&<query params>` (foreign uri)

`<query params>`
* level:
  * `1`: data properties of the collection (default)
  * `2`: + object properties of the collection

`<filter params>`
* institutions=`comma separated list of institution URIs or IDs`
* sets=`comma separated list of set URIs or IDs`
* searchProfile=`id of a search profile`

`<limit params>`
* limit=`nr of collections to return`
* last=`offset or last seen collection`

## concepts

`.../concepts?<selection params>&<filter params>&<projection params>&<order params>&<limit params>`

`.../concept/{id}?<projection params>` (native uri)

`.../concept?uri={uri}&<projection params>` (foreign uri)

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
* institutions=`comma separated list of institution URIs or IDs`
* sets=`comma separated list of set URIs or IDs`
* conceptSchemes=`comma separated list of concept scheme URIs or IDs`
* collections=`comma separated list of collection URIs or IDs`
* searchProfile=`id of a search profile`
* dateSubmitted=`xsd:duration and some shortcuts (?)`
* modified=`xsd:duration and some shortcuts (?)`
* dateAccepted=`xsd:duration and some shortcuts (?)`
* openskos:deleted=`xsd:duration and some shortcuts (?)`
* statuses=`comma separated list of statuses`
  * `candidate`
  * `approved`
  * `redirected`
  * `not_compliant`
  * `rejected`
  * `obsolete`
  * `deleted`
* creator=`comma separated list of user URIs or IDs`
* openskos:modifiedBy=`comma separated list of user URIs or IDs`
* openskos:acceptedBy=`comma separated list of user URIs or IDs`
* openskos:deletedBy=`comma separated list of user URIs or IDs`

`<projection params>`
* level=`which properties to return`
  * `1`: data properties of the concepts (default)
  * `2`: + object properties of the concepts
* props=`comma separated list of`
  * default: uri,prefLabel,definition
  * all
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
    * broaderTransitive
    * narrowerTransitive
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
  * dates
    * dateSubmitted
    * modified
    * dateAccepted
    * openskos:deleted
  * users
    * creator
    * openskos:modifiedBy
    * openskos:acceptedBy
    * openskos:deletedBy
  * {user relation types}
  * {user properties}
* lang=`comma separated list of known language codes (BCP 47)`
  * if not specified all languages are projected (default)
* skosxl=`yes or no` (default: follow configuration of the tenant)
  NOTE: `skosxl=yes` can result in a `400` if one of the tenants doesn't support SKOS-XL

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
* dateSubmitted
* modified
* dateAccepted
* openskos:deleted

`<limit params>`
* limit=`nr of concepts to return`
* last=`last seen concept`

## autocomplete

`.../autocomplete?<selection params>&<projection params>&<filter params>`

NOTE: or
`.../{concepts|labels}/autocomplete?<selection params>&<projection params>&<filter params>`

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
  * literalForm
  * label(@{lang})?
    * prefLabel(@{lang})?
    * altLabel(@{lang})?
    * hiddenLabel(@{lang})?
  * notation

`<filter params>`
* institutions=`comma separated list of institution URIs or IDs`
* sets=``comma separated list of set URIs or IDs``
* conceptSchemes=`comma separated list of concept scheme URIs or IDs`
* collections=`comma separated list of collection URIs or IDs`
* searchProfile=`id of a search profile`
* dateSubmitted=`xsd:duration and some shortcuts (?)`
* modified=`xsd:duration and some shortcuts (?)`
* dateAccepted=`xsd:duration and some shortcuts (?)`
* openskos:deleted=`xsd:duration and some shortcuts (?)`
* statuses=`comma separated list of statuses`
  * `candidate`
  * `approved`
  * `redirected`
  * `not_compliant`
  * `rejected`
  * `obsolete`
  * `deleted`
* creator=`comma separated list of user URIs or IDs`
* openskos:modifiedBy=`comma separated list of user URIs or IDs`
* openskos:acceptedBy=`comma separated list of user URIs or IDs`
* openskos:deletedBy=`comma separated list of user URIs or IDs`
* skosxl=`yes or no` (default: follow configuration of the tenant)
  * TODO: is this nice?

## labels

NOTE: SKOS-XL

`.../labels?<selection params>&<filter params>&<projection params>&<order params>&<limit params>`

NOTE: maybe only when `skosxl:label` are first class citizens

`.../label/{id}?<projection params>` (native uri)

`.../label?uri={uri}&<projection params>` (foreign uri)

`<projection params>`
* level=`which properties to return`
  * `1`: data properties of the concepts (default)
  * `2`: + object properties of the concepts
* props=`comma separated list of`
  * default: uri,literalForm
  * all
  * uri
  * literalForm(@{lang})?
  * openskos:isPrefLabelOf (inverse of prefLabel)
  * openskos:isAltLabelOf (inverse of altLabel)
  * openskos:isHiddenLabelOf (inverse of hiddenLabel)
  * labelRelation
  * openskos:set [TODO: check]
  * openskos:institution (NOTE: is currently openskos:tenant)
  * status
    * NOTE: not supported at the moment, TODO discuss if labels should be first class citizens
    * "`alive`"
    * `deleted`
  * dates
    * modified
    * NOTE: maybe also have?
      * dateSubmitted
      * openskos:deleted
  * users
    * NOTE: not supported at the moment, TODO discuss if labels should be first class citizens
    * creator
    * openskos:modifiedBy
    * openskos:acceptedBy
    * openskos:deletedBy
  * {user relation types}
  * {user properties}
* lang=`comma separated list of known language codes (BCP 47)`
  * if not specified all languages are projected (default)

## relations

`.../relations?<selection params>&<projection params>&<limit params>`

`<selection params>`
* subject=`uri`
* types=`comma separated list of relation types`
  * semanticRelation
    * broader
    * narrower
    * related
    * broaderTransitive
    * narrowerTransitive
    * mappingRelation
      * closeMatch
      * exactMatch
      * broadMatch
      * narrowMatch
      * relatedMatch
  * inScheme
    * topConceptOf
  * isReplacedBy (NOTE: currently not yet a triple in OpenSKOS)
  * replaces (NOTE: currently not yet a triple in OpenSKOS)
  * openskos:inCollection (OpenSKOS specific inverse of skos:member) [NOTE: currently inSkosCollection]
  * openskos:inSet (NOTE: currently openskos:set)
  * openskos:tenant
  * member
  * hasTopConcept
  * prefLabel
  * altLabel
  * hiddenLabel
  * openskos:isPrefLabelOf (inverse of prefLabel)
  * openskos:isAltLabelOf (inverse of altLabel)
  * openskos:isHiddenLabelOf (inverse of hiddenLabel)
  * labelRelation
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

`filter params`
TODO: what does it mean for subject and object?

`<limit params>`
* limit=`nr of subjects to return`
* last=`last seen subject`

## relation types

## users

## roles

## search profiles

NOTE: conditions in a search profile might potentially clash with other selection/filter parameters.

## statuses

NOTE: GET only, as the list of statuses is fixed

## prefixes

NOTE: the namespaces are currently hardcoded (not in the MySQL anymore or in application.ini), so to make the set extensible (again) will require some work.