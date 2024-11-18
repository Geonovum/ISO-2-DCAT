# Test input

Files from Nationaal Georegister.

[Stads- en Dorpsgezichten](https://nationaalgeoregister.nl/geonetwork/srv/dut/catalog.search#/metadata/4e2ef670-cddd-11dd-ad8b-0800200c9a66)

Dataset from RCE. The XML validates against the Geonovum [validator for iso-19115-nl](https://validatie.geostandaarden.nl/etf-webapp/testprojects?testdomain=Metadata)

One edit has been made to the xml file to make sure it works as intended as a testfile for the iso-19139-to-dcat-ap.xsl

original:

```xml
<gmd:MD_Identifier>
    <gmd:code>
        <gmx:Anchor xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="https://services.rce.geovoorziening.nl/www/download/nl.xml">e39bd6e0-7651-11e0-a1f0-0800200c9a62</gmx:Anchor>
    </gmd:code>
</gmd:MD_Identifier>
```

adapted:

```xml
<gmd:MD_Identifier>
    <gmd:code>
        <gmx:Anchor xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="https://services.rce.geovoorziening.nl/www/download/e39bd6e0-7651-11e0-a1f0-0800200c9a62">e39bd6e0-7651-11e0-a1f0-0800200c9a62</gmx:Anchor>
    </gmd:code>
</gmd:MD_Identifier>
```

Note: this does not satisfy the requirements completely because the xlink:href is not resolvable...

---

[Basisregistratie Adressen en gebouwen (BAG)](https://nationaalgeoregister.nl/geonetwork/srv/dut/catalog.search#/metadata/aa3b5e6e-7baa-40c0-8972-3353e927ec2f)

Dataset from Kadaster. The XML validates against the Geonovum [validator for iso-19115-nl](https://validatie.geostandaarden.nl/etf-webapp/testprojects?testdomain=Metadata)
