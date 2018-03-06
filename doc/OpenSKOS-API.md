# OpenSKOS 2 RESTful API

## TODO

- [X] SPARQL paging experiments
  - [X] use LIMIT and OFFSET
  - [ ] use a last seen concept (DECISION: No)
  - [/] can we use the [hydra paging (`hydra:PartialCollectionView`)](http://www.hydra-cg.com/spec/latest/core/#collections)? (envisioned part of the NDE generic API)
- [ ] how to handle URIs
  - e.g., register prefix in `application.ini` (see [prefixes](#prefixes)
  - user specified relation types
  - user provided properties
  - refering to OpenSKOS resources by their foreign URIs
- [X] check if we follow RFC 6570 correctly (NO)
- [X] what do we use for refering to internal resources
  - [X] `uri` or `id` or both
  - [ ] allow prefixes (see above)
- [ ] add cardinalities
- [X] if an higher level includes concepts, e.g., for collections and concept schemes, also include the concept `<projection params>.props`? NO
- [X] camelCase in params, e.g., `conceptScheme`, but not in endpoints, e.g., `.../conceptscheme`? OK as it is
- [ ] check how set and institution are expressed in OpenSKOS 2, e.g., `openskos:set` or `openskos:institution` (see concept `<projection params>`)?
- [X] analyze if major (read) functionality of the editor is covered in an appropriate amount of requests, e.g., a list should not require a call for each concept
- [/] return codes
  - 200 everything OK
  - 400 if other parameters have a clash with search profile
  - 401 or 403 if unauthorized
  - 404 a resource is not found
  - 405 method not allowed, e.g., a PUT on a list
  - 406 unsupported RDF serialization
  - 500 and higher: oops
- [ ] return representations (at least JSON-LD, RDF/XML, TriG/TriX, (incl. Hydra paging vocab), HTML)
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
- [/] concepts is used as the main discussion endpoint, sync with other endpoints
- [X] support for SKOS-XL labels
- [ ] check consistent use of institution vs tenant
- [ ] discuss the first class citizenship of `skosxl:Labels`
- [ ] add format parameter
- [ ] lists of many (all?) resources are filtered based on access rights, e.g., admin can see all, users can only see what belongs to their institution (or their searchProfile allows)

## Foundation

* content negotiation
  * also a parameter (?format=)
  * incl. HTML (containing the same info as the RDF serializations)
  * JSON-LD instead of proprietary JSON
  * support at least JSON-LD, RDF/XML, TriG/TriX, (incl. Hydra paging vocab), HTML
  * all should return the same info, which is controlled (potentially) by the projection parameters
* API key in header and use HTTPS always
  * good for security, bad for traceability (look at server logs and see who has done what)
* focus on read-only first
* hide implementation details (Lucene query language)
* using just OpenSKOS generated URIs should work out of the box
* using foreign URIs (external SKOS imported into OpenSKOS) should work out of the box (just don't use id endpoints)

## Does the new API remedy critisism on the old API?
- [X] the API should identify **resources** instead of **actions** (TODO: maybe autocomplete is still too action like)
- [/] use the same URI for all HTTP CRUD methods (NOTE: currently the API only defines the GET methods, but the same (singular) endpoints should support the CRUD methods in the future. POSTs could happen on the plural endpoints as well.)
- [/] support content negotiation, so you have neat (independent of format) conceptual resource URIs, i.e., not representation specific URIs (NOTE: a foundation of the new API is support content negotiation. However, support for specifying format in a uri part breaks this, so only support content negotiation or a format parameter!)
- [/] client can interact choosing one of the supported RDF representations (NOTE: for the CRUD operations all RDF serializations supported for GET should also be supported for PUT and POST. All representations should contain the same information! HTML is only supported for GET.)
- [X] describe the openskos namespace, so the responses are self describing
- [/] don't pass API keys in the body (NOTE: API keys should always be passed in HTTPS based requests)
- [X] use JSON-LD, propriety JSON is not self descriptive 
- [/] HATEOAS (NOTE: there might not be URIs just literals for some entities (e.g., NAA) due to legacy)
- [/] HTML representations should provide HATEOAS links
- [?] replace API keys by OAuth(2)
- [?] use the Linked Data Platform (NOTE: at least paging collides with Hydra paging)
- [?] use Triple Pattern Fragments

## Does the new API provide good coverage for the functionality provided by the editor?

- [X] get the user profile
  1. `../user`
- [X] list concept schemes
  1. `.../conceptschemes?level=1&institions=<id>`
  1. or with a search profile: `.../conceptschemes?level=1&searchProfile=<id>`
- [X] list concepts in a concept scheme
  1. ```.../concepts?conceptScheme=<id or URI>&props=prefLabel```
- [X] show a selected concept and its relations
  1. info about the concept: ```.../concept/<id>?level=2```
  2. relations from the concept: ```.../relations?subject=<id>&types=semanticRelation&props=prefLabel```
  3. relations to the concept: ```.../relations?object=<id>&types=semanticRelation&props=prefLabel```
  4. user specific relations to the concept: ```.../relations?object=<id>&types=mi:fasterThan&props=prefLabel```
- [X] search for a concept
  1. ```.../concepts?text=<search terms>&props=prefLabel```
  1. or with a search profile: ```.../concepts?text=<search terms>&props=prefLabel&searchProfile=<id>```
- [X] list of search profiles
  1. `.../searchprofiles`
- [x] info of a search profile
  1. `.../searchprofile/<id>`
  2. list of concept schemes: `.../conceptschemes?level=1`
  3. list of sets: `.../sets?level=1`
  4. list of institutions: `.../institutions?level=1`
- [X] get the info on the institution
  1. `.../institution/<id>?level-1` (NOTE: id is known from the user profile) (TODO: check if a higher level is needed due to VCard)
- [X] get the list of sets
  1. `.../sets?institutions=<id>&level=1`
- [X] get the info on a set
  1. `.../set/<id>?level=1` (TODO: check if a higher level is needed due to VCard)
- [X] get the list of concept schemes
  1. `.../conceptschemes?institutions=<id>&level=1`
- [X] get the info on a concept scheme
  1. `.../conceptscheme/<id>?level=1`

## Notation

The API endpoints are described using [URI templates](http://www.rfc-editor.org/info/rfc6570). However, sets of parameters are pretty common, we name these groups and use `<parameter group name>` to refer to them in an URI.

## common variables

* {uri}=`the foreign URI of an resource` 
* {id}=`the native ID of an resource`
* {lang}=`known language code (BCP 47)`

## institutions

`.../institutions?<query params>&<limit params>`

`.../institution/{id}?<query params>` (native uri)

`.../institution?uri={uri}&<query params>` (foreign uri)

`<query params>`
* level:
  * `1`: data properties of the institution (default)
  * `2`: + object properties of the institution
  * `3`: + data properties of the set
  * `4`: + object properties of the set

NOTE: the OpenSKOS editor should never use level `3` or `4`, they're more interesting for external LD parties

`<limit params>`
* limit=`nr of institutions to return`
* offset=`offset seen institution`

## sets

`.../sets?<query params>&<filter params>&<limit params>`

`.../set/{id}?<query params>` (native uri)

`.../set?uri={uri}&<query params>` (foreign uri)

`<query params>`
* level:
  * `1`: data properties of the set (default)
  * `2`: + object properties of the set
  * `3`: + data properties of the concept scheme or collection
  * `4`: + object properties of the concept scheme or collection

NOTE: the OpenSKOS editor should never use level `3` or `4`, they're more interesting for external LD parties

`<filter params>`
* institutions=`comma separated list of institution URIs or IDs`

`<limit params>`
* limit=`nr of sets to return`
* offset=`offset of last seen set` (NOTE: starts a 0)

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

NOTE: the OpenSKOS editor should never use level `3` or `4`, they're more interesting for external LD parties

`<filter params>`
* institutions=`comma separated list of institution URIs or IDs`
* sets=`set`
* searchProfile=`id of a search profile`

`<limit params>`
* limit=`nr of concept schemes to return`
* offset=`offset of last seen concept scheme` (NOTE: starts at 0)

## collections

`.../collections?<query params>&<filter params>&<limit params>`

`.../collection/{id}?<query params>` (native uri)

`.../collection?uri={uri}&<query params>` (foreign uri)

`<query params>`
* level:
  * `1`: data properties of the collection (default)
  * `2`: + object properties of the collection
  * `3`: + data properties of the concept
  * `4`: + object properties of the concept

NOTE: the OpenSKOS editor should never use level `3` or `4`, they're more interesting for external LD parties

`<filter params>`
* institutions=`comma separated list of institution URIs or IDs`
* sets=`comma separated list of set URIs or IDs`
* searchProfile=`id of a search profile`

`<limit params>`
* limit=`nr of collections to return`
* offset=`offset of last seen collection` (NOTE: starts at 0)

## concepts

`.../concepts?<selection params>[?]&<filter params>&<projection params>&<order params>&<limit params>&<format params>`

`.../concept/{id}?<projection params>` (native uri)

`.../concept?uri={uri}&<projection params>` (foreign uri)

`<selection params>`
* text[1]=`search terms`
  * support for
    * strings: ab c
    * wildcard: \*a a\* a*b
    * boolean: a AND b OR NOT c
    * brackets: (a OR b) AND c
    * phrases: "ab c"
    * escapes: \\" \\* \\\\
* fields[?]=`comma separated list of`
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
* offset=`offset of last seen concept` (NOTE: starts at 0)

## autocomplete

`.../concepts/autocomplete?<selection params>&<projection params>&<filter params>`

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

`.../labels/autocomplete?<selection params>&<projection params>&<filter params>`

`<selection params>`
* text=`substring to match case insensitively`

`<projection params>`
* props=`comma separated list of`
  * default: uri,literalForm
  * uri
  * literalForm

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

TODO: lots of these bookkeeping-based filters depend on SKOS-XL labels being first class citizens!

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

`<filter params>`
TODO: what does it mean for subject and object?

`<limit params>`
* limit=`nr of subjects to return`
* offset=`offset of last seen subject` (NOTE: starts at 0)

## relation types

`.../relationtypes`

`.../relationtype/{id}` (native uri)

`.../relationtype?uri={uri}` (foreign uri)

## users

`.../users?<query params>&<limit params>`

`.../user/{id}`

`.../user`

NOTE: the user endpoint without ID can be used to retrieve the profile of the logged in user, i.e., identified by her credentials or API key.

NOTE: these endpoints will only return info if the user has admin rights, or asks for her own profile.

`<query params>`
* level:
  * `1`: data properties of the user (default)
  * `2`: + object properties of the user

`<limit params>`
* limit=`nr of institutions to return`
* offset=`offset of last seen user` (NOTE: starts at 0)

## roles

`.../roles`

## search profiles

`.../searchprofiles`

`.../searchprofile/{id}`

NOTE: conditions in a search profile might potentially clash with other selection/filter parameters.

## statuses

`.../statuses`

NOTE: GET only, as the list of statuses is fixed

## prefixes

`.../prefixes`

NOTE: the namespaces are currently hardcoded (not in the MySQL anymore or in `application.ini`), so to make the set extensible (again) will require some work. Extension might only be possible via `application.ini`, so the endpoint could be only a GET.

## ping

`.../ping`

Gives info about the OpenSKOS instance, e.g., version, health.

## vocab

`.../vocab`

Gives an RDFS resource describing properties and classes in the openskos namespace.

NOTE: might have to be only available on the openskos.org, i.e., http://openskos.org/vocab. This should correspond with the openskos namespace URI (`xmlns:openskos="http://openskos.org/vocab#"`). However, at the moment this is `http://openskos.org/xmlns#`.