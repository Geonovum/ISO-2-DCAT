# DCAT-AP-NL 3 Validatie

Het jupyter notebook in deze folder kan gebruikt worden voor de validatie van DCAT-AP-NL 3 bestanden.

Dit notebook gebruikt:
- json, rdflib, pyshacl, dotenv, re, lxml, pathlib, requests, os, pandas, datetime

### Input:
een json configuratiefile in de vorm:
```
[
  {
    "naam": "Vaarwegmarkeringen Nederland",
    "type": "Dataset (valide)",
    "url": "https://ngr.acceptatie.nationaalgeoregister.nl/geonetwork/srv/api/records/be1b1514-8d1f-48e1-9624-fee9b784138b/formatters/dcat-ap-nl-3?output=xml",
    "focus_node": "https://ngr.acceptatie.nationaalgeoregister.nl/geonetwork/srv/api/records/be1b1514-8d1f-48e1-9624-fee9b784138b#resource"
  }
]
```

Stappen:

- downloaden van RDF/XML file van de url locatie
- converteren van rdf/xml naar ttl => dit is meteen een test of de rdf/xml valide rdf statements bevat
- pyshacl validatie op basis van dcat-ap-SHACL.ttl en dcat-ap-nl-SHACL.ttl (dus niet uitegebreid met klassebereik)
    - validatie alleen op de 'focusnode' van de betreffende file, afkomstig uit de json configuratie
- sparql query om, indien er fouten zijn, hier een compact overzicht uit op te maken
- markdown file met rapportage op:
    - valide rdf
    - resultaat van de shacl validatie