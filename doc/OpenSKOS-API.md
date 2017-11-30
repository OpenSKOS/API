# OpenSKOS RESTful API

## foundation

* content negotiation
  * also a parameter (?format=) or uri part (.n3)
  * incl. HTML (on equal with JSON/RDF)
  * JSON-LD instead of proprietary JSON
* API key in header
  * good for security, bad for traceability (look at server logs and see who has done what)
  * focus on read-only first
* hide implementation details (Lucene query language)

## variables

* {uri}
* {id}
* {lang}=`known language code (BCP 47)`

## institutions

## sets

## concept schemes

## collections

`.../collections?<query params>`

* limit=`nr of collections to return`
* last=`offset or last seen collection`

`.../collections/{id}?<query params>` (native uri)

* level:
  * `1`: data properties of the collection (default)
  * `2`: + object properties of the collection
  * `3`: + data properties of the concept
  * `4`: + object properties of the concept
* limit=`nr of concepts to return`
* last=`last seen concept` [TODO: SPARQL experiment]

`.../collections/?uri={uri}<query params>` (foreign uri)

## concepts

* selection params
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

* projection params
  * level:
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

* order params
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

* limit params
  * limit=`nr of concepts to return`
  * last=`last seen concept`

`.../concepts?<selection params><projection params><order params><limit params>`

`.../concepts/{id}?<projection params>` (native uri)

`.../concepts?uri={uri}<projection params>` (foreign uri)

## relations

* selection params
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

* projection params
  * props=`comma separated list of`
    * default: uri
    * uri
    * title(@{lang})?
    * label(@{lang})?
      * prefLabel(@{lang})?
      * altLabel(@{lang})?
      * hiddenLabel(@{lang})?

* limit params
  * limit=`nr of subjects to return`
  * last=`last seen subject`

`.../relations?<selection params><projection params><limit params>`

## relation types (??)

## user (??)

## status (??)