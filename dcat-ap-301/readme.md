# verschil analyse DCAT-AP 3.0.0 -> 3.0.1

handmatig gecheckt met changelog uit de repo:
https://semiceu.github.io/DCAT-AP/releases/3.0.1/CHANGELOG.html

Maar het kan ook met SPARQL...

Aanpak:
- GraphDB
    - graph met 3.0.0 release
    - graph met 3.0.1 release

SPARQL Query:
```
PREFIX shacl: <http://www.w3.org/ns/shacl#>
PREFIX dcat: <http://www.w3.org/ns/dcat#>
PREFIX s0: <https://semiceu.github.io/DCAT-AP/releases/3.0.0/shacl/dcat-ap-SHACL.ttl#>
PREFIX s1: <https://semiceu.github.io/DCAT-AP/releases/3.0.1#>

SELECT *
WHERE {
    {
        # ==========================================================
        # PART 1: V3.0.0 LEFT JOIN V3.0.1
        # Shows V3.0.0 properties and any matching V3.0.1 properties
        # ==========================================================
        {
            # Subquery A: Aggregate V3.0.0 data (The 'Left' side)
            SELECT ?path
                (MAX(COALESCE(?minC_0, 0)) AS ?min_300)
                (MIN(?maxC_0) AS ?max_300)
                (GROUP_CONCAT(DISTINCT ?classV_0; separator=" / ") AS ?class_300)
                (GROUP_CONCAT(DISTINCT ?dataTypeV_0; separator=" / ") AS ?datatype_300)
                (GROUP_CONCAT(DISTINCT ?nodeKV_0; separator=" / ") AS ?nodekind_300)
            WHERE {
                GRAPH <https://semiceu.github.io/DCAT-AP/releases/3.0.0#> { # <-- REPLACE GRAPH URI
                    s0:dcat:CatalogShape shacl:property ?ps_0 .
                    ?ps_0 shacl:path ?path .
                    OPTIONAL { ?ps_0 shacl:minCount ?minC_0 . }
                    OPTIONAL { ?ps_0 shacl:maxCount ?maxC_0 . }
                    OPTIONAL { ?ps_0 shacl:class ?classV_0 . }
                    OPTIONAL { ?ps_0 shacl:datatype ?dataTypeV_0 . }
                    OPTIONAL { ?ps_0 shacl:nodeKind ?nodeKV_0 . }
                }
            }
            GROUP BY ?path
        }
        OPTIONAL {
            # Subquery B: Aggregate V3.0.1 data (The 'Right' side, for joining)
            {
                SELECT ?path
                    (MAX(COALESCE(?minC_1, 0)) AS ?min_301)
                    (MIN(?maxC_1) AS ?max_301)
                    (GROUP_CONCAT(DISTINCT ?classV_1; separator=" / ") AS ?class_301)
                    (GROUP_CONCAT(DISTINCT ?dataTypeV_1; separator=" / ") AS ?datatype_301)
                    (GROUP_CONCAT(DISTINCT ?nodeKV_1; separator=" / ") AS ?nodekind_301)
                WHERE {
                    GRAPH <https://semiceu.github.io/DCAT-AP/releases/3.0.1#> { # <-- REPLACE GRAPH URI
                        s1:dcat:CatalogShape shacl:property ?ps_1 .
                        ?ps_1 shacl:path ?path .
                        OPTIONAL { ?ps_1 shacl:minCount ?minC_1 . }
                        OPTIONAL { ?ps_1 shacl:maxCount ?maxC_1 . }
                        OPTIONAL { ?ps_1 shacl:class ?classV_1 . }
                        OPTIONAL { ?ps_1 shacl:datatype ?dataTypeV_1 . }
                        OPTIONAL { ?ps_1 shacl:nodeKind ?nodeKV_1 . }
                    }
                }
                GROUP BY ?path
            }
        }
    }
    UNION
    {
        # ==========================================================
        # PART 2: V3.0.1 MINUS V3.0.0
        # Shows only properties that are NEW in V3.0.1
        # ==========================================================
        {
            # Subquery C: Aggregate V3.0.1 data (The 'new' properties)
            SELECT ?path
                (MAX(COALESCE(?minC_1, 0)) AS ?min_301)
                (MIN(?maxC_1) AS ?max_301)
                (GROUP_CONCAT(DISTINCT ?classV_1; separator=" / ") AS ?class_301)
                (GROUP_CONCAT(DISTINCT ?dataTypeV_1; separator=" / ") AS ?datatype_301)
                (GROUP_CONCAT(DISTINCT ?nodeKV_1; separator=" / ") AS ?nodekind_301)
            WHERE {
                GRAPH <https://semiceu.github.io/DCAT-AP/releases/3.0.1#> { # <-- REPLACE GRAPH URI
                    s1:dcat:CatalogShape shacl:property ?ps_1 .
                    ?ps_1 shacl:path ?path .
                    OPTIONAL { ?ps_1 shacl:minCount ?minC_1 . }
                    OPTIONAL { ?ps_1 shacl:maxCount ?maxC_1 . }
                    OPTIONAL { ?ps_1 shacl:class ?classV_1 . }
                    OPTIONAL { ?ps_1 shacl:datatype ?dataTypeV_1 . }
                    OPTIONAL { ?ps_1 shacl:nodeKind ?nodeKV_1 . }
                }
            }
            GROUP BY ?path
        }
        MINUS {
            # Subquery D: Exclude any properties that also exist in V3.0.0
            SELECT ?path
            WHERE {
                GRAPH <https://semiceu.github.io/DCAT-AP/releases/3.0.0#> { # <-- REPLACE GRAPH URI
                    s0:dcat:CatalogShape shacl:property ?ps_0 .
                    ?ps_0 shacl:path ?path .
                }
            }
        }
    }
    
    # ==========================================================
    # FINAL FILTER: Only keep rows where a constraint has changed
    # ==========================================================
     FILTER (
        # 1. Properties that were ADDED in 3.0.1 (3.0.0 constraints are unbound)
        # IS_BOUND(?min_301) is sufficient since all other 3.0.0 columns will be unbound too.
        !BOUND(?min_300)

        # OR 2. Properties that were REMOVED in 3.0.1 (3.0.1 constraints are unbound)
        || !BOUND(?min_301)

        # OR 3. Properties where a Cardinality constraint CHANGED
#        || ?min_300 != ?min_301
        || (!BOUND(?max_301) && BOUND(?max_300))
        || ?max_300 != ?max_301

        # OR 4. Properties where a Value constraint CHANGED
        || ?class_300 != ?class_301
        || ?datatype_300 != ?datatype_301
        || ?nodekind_300 != ?nodekind_301
    )
}
ORDER BY ?path
```
(met dank aan gemini... en daarna zelf dubbel checken en valideren...)
