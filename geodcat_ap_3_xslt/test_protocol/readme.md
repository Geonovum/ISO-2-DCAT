# Test Protocol

## Objectives

Support the SEMIC community to release an improved GeoDCAT-AP 3 specification and XSLT transformation.

- test we if we can transform iso-19115-nl compliant data to GeoDCAT-AP to fulfill HVD requirements
- test to what extent the geodcat-ap xslt is usable for this task and if the xslt is re-usable for the iso-19115-nl to dcat-ap-nl transformation
- examine GeoDCAT-AP and evaluate the dcat-ap-nl compatibility with it.

## procedure

- Select a limited set of iso-19115-nl compliant metadata descriptions from the Dutch NGR as test set.
- Perform a transformation to GeoDCAT-AP and study the output

See individual readme's in the seperate folders for details.

As primary 'techstack' python and Jupyter Notebook are chosen:

    - rdflib
    - pyshacl
    ~~-lxml~~ 
    - saxonche

## Findings

1) XSLT v2 support in Python

    The only XSL transformer to support XSLT v2 seems to be the Saxon processor. The 'standard' python xml modules (lxml or ElementTree) only support v1.
    Once this is clear, it is relatively straightforward to use saxonche for XSLT v2 support in Python.

2) XSLT modularity and version management

    - The GeoDCAT-AP XSLT is written as one massive XSLT file. While this is a 'simple' approach in reusing templates and functions within the file, the file itself becomes very large and complex to understand.
    A more modular approach might provide more flexibility in supporting different scenarios.
    - It is not immediately obvious from the repository structure which XSLT is applicable to GeoDCAT-AP 3.0.0 and whether there are previous versions or not.
    I would have expected branches or folders for separate versions for example.

3) iso-19139-to-dcat-ap github issues:

    - 58 [blank nodes for distribution, dataservice and catalogrecord](https://github.com/SEMICeu/iso-19139-to-dcat-ap/issues/58)
    - 59 [mapping of Distribution and DataService](https://github.com/SEMICeu/iso-19139-to-dcat-apissues/59)
    - 60 [MD_Keywords to dcat:theme transformation is incomplete](https://github.com/SEMICeu/iso-19139-to-dcat-ap/issues/60)

4) GeoDCAT-AP Shacl design

    The choice for one massive shacl file with all constraints makes it quite complex to grok the file.
    The design decision to use UUID's for the unique identification of individual shacl constraints might make sense for a 'computer generated' approach, but it does diminish readability of the file.
    Personally, I quite like the design decisions that are made in the dcat-ap-nl profile (which I had no involvement in).

5) GeoDCAT-AP github issues:

    - 141 [misalignment between GeoDCAT-AP dct:Standard properties and XSLT transformation rules](https://github.com/SEMICeu/GeoDCAT-AP/issues/141)
    - 142 [create the geodcat-ap shacl file as an extension to the dcat-ap shacl file](https://github.com/SEMICeu/GeoDCAT-AP/issues/142)

6) General observations:

    In the UML vs RDF realm of information modelling we are quite aware of the 'paradigm shift' and 'Closed World' vs 'Open World' modelling approach and it's consequences.
    For Metadata models between ISO/XML and DCAT we are experiencing similar differences.
    In a typical iso-19115 dataset description all elements (including catalogs and distributions) are described within the context of the individual file.
    However, DCAT is fundamentally positioned as a standard to describe 'federated datastructures' therefore datasets, distributions etc are separate classes.
    The XSLT approach is not 'aware' of this and describes the 'federated structure' from a 'single file' perspective, this is bound to lead to unintended consequences.
