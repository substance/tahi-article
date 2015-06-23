# Tahi Article Specification

The goal of this spec is to define the possible structure and markup of a Tahi-Article. This format is used as an input and output format for the Tahi editor and the document conversion service iHat.

Tahi articles live in the database, and are fragmented to different tables and fields. For that reason we defined rules for individual sections on the persistence level.

*Note: This spec is a work in progress*

## Persistence Level

**Manuscript body** (`Paper.body`)

```html
  <h1 id="h1">A heading</h1>
  <p id="p1">
    Some <em id="em13">annotated</em> content (see <cite id="bibref13" typeof="bibref" data-rid="bib13">Doe, 2010</cite> and <cite id="figref9" typeof="figref" data-rid="fig1">Figure 1</cite>).
  </p>
  <p id="p2">
    When
    <math>
      <mi>a</mi><mo>&#x2260;</mo><mn>0</mn>
    </math>,
    there are two solutions to
    <math>
      <mi>a</mi><msup><mi>x</mi><mn>2</mn></msup>
      <mo>+</mo> <mi>b</mi><mi>x</mi>
      <mo>+</mo> <mi>c</mi> <mo>=</mo> <mn>0</mn>
    </math>
    and they are
  </p>
  <!-- Top level math elements are considered block formulas -->
  <math id="f1" typeof="formula">
    <mi>x</mi> <mo>=</mo>
    <mrow>
      <mfrac>
        <mrow>
          <mo>&#x2212;</mo>
          <mi>b</mi>
          <mo>&#x00B1;</mo>
          <msqrt>
            <msup><mi>b</mi><mn>2</mn></msup>
            <mo>&#x2212;</mo>
            <mn>4</mn><mi>a</mi><mi>c</mi>
          </msqrt>
        </mrow>
        <mrow> <mn>2</mn><mi>a</mi> </mrow>
      </mfrac>
    </mrow>
    <mtext>.</mtext>
  </math>
  <!-- Simple table -->
  <table id="t1">
    <thead id="t1_h">
      <tr id="t1_1"><th id="t1_1_1">A</th><th id="t1_1_2">B</th><th id="t1_1_3">C</th><th id="t1_1_4">D</th><th id="t1_1_5">E</th><th id="t1_1_6">F</th></tr>
    </thead>
    <tbody id="t1_b">
      <tr id="t1_2"><td id="t1_2_1">1</td><td id="t1_2_2">2</td><td id="t1_2_3">3</td><td id="t1_2_4">4</td><td id="t1_2_5">5</td><td id="t1_2_6">6</td></tr>
      <tr id="t1_3"><td id="t1_3_1">7</td><td id="t1_3_2">8</td><td id="t1_3_3">9</td><td id="t1_3_4">10</td><td id="t1_3_5">11</td><td id="t1_3_6">2</td></tr>
      <tr id="t1_4"><td id="t1_4_1">13</td><td id="t1_4_2">14</td><td id="t1_4_3">15</td><td id="t1_4_4">16</td><td id="t1_4_5">17</td><td id="t1_4_6">18</td></tr>
      <tr id="t1_5"><td id="t1_5_1">19</td><td id="t1_5_2">20</td><td id="t1_5_3">21</td><td id="t1_5_4">22</td><td id="t1_5_5">23</td><td id="t1_5_6">24</td></tr>
      <tr id="t1_6"><td id="t1_6_1">25</td><td id="t1_6_2">26</td><td id="t1_6_3">27</td><td id="t1_6_4">28</td><td id="t1_6_5">29</td><td id="t1_6_6">30</td></tr>
    </tbody>
  </table>
  <h1 id="h2">Top level</h1>
  <!-- include external resource (figure) -->
  <div typeof="figinc" data-rid="fig1"></div>
```

Allowed tags: `<p>`, `<h1>`, `<h2>`, `<h3>`, `<em>`, `<strong>`, `<cite>`, `<div class="figinc">`

The manuscript body uses common HTML tags for representing headings, paragraphs and annotations. For expressing figure references and citations we use the `<cite>` tag in combination with data attributes. We make use of some RDFa attributes for assigning property and type names to our elements. In order to include a figure in a certain place we just use a placeholder element of the type "`figinc`"

**Title** (`Paper.title`)

```html
The <em>TAHI</em> Article Format
```

Allowed tags: `<em>`, `<strong>`

**Abtract** (`Paper.abstract`)

```html
Article abstract that can be <strong>annotated</strong>
```

Allowed tags: `<em>`, `<strong>`


**Figure caption** (`Figure.caption`)

While most data fields (title, url, etc.) of a figure are stored as strings in the database, we use HTML for the figure caption.

Example:

```html
Figure caption that can be <strong>annotated</strong>
```

Allowed tags: `<em>`, `<strong>`, `<cite>`


## Exchange Level

The previous section defined allowed HTML markup for individual properties of a Tahi document. In order to exchange a complete snapshot of a document, including all resources such as figures and bibliographic entries we need a combined representation.

Here's a possible representation in JSON, that could be used to transfer data to the editor.

```json
{
  "id": "10",
  "title": "The <em>TAHI</em> Article Format",
  "abstract": "Article abstract that can be <strong>annotated</strong>",
  "body": "<h1 id=\"h1\">A heading</h1>...",
  "resources": {
    "fig1": {
      "type": "fig",
      "caption": "annotated <em>caption</em>"
    },
    "bib1": {}
  }
}
```

An interesting way could also be providing the complete data as an HTML document.

```html
<!DOCTYPE html>
<html lang="en"> 
  <head>
    <!-- title is only there to calm down the validator. do not edit -->
    <title>🐰</title>
    <meta charset="utf-8" /> 
  </head>
  <body>
    <header>
      <!-- we use rdfa property attribute to mark object props -->
      <div typeof="meta" id="meta">
        <h1 property="title">The <em>TAHI</em> Article Format</h1>
        <div property="abstract">Article abstract that can be <strong>annotated</strong></div>
      </div>
      <div typeof="fig" id="fig1">
        <div property="caption">Figure caption <em>annotated</em></div>
        <div property="src">fig1.png</div>
        <div property="previewSrc">fig1_preview.png</div>
        ...
      </div>
      <div typeof="bib" id="bib1">
        <script type="application/json" data-format="citeproc">
          {
            "subtitle": [],
            "issued": {
              "year": 2013,
              "month": 8
            },
            "score": 1,
            "prefix": "http://id.crossref.org/prefix/10.1109",
            "author": [
              {
                "family": "Lughofer",
                "given": "Edwin"
              },
              {
                "family": "Buchtala",
                "given": "Oliver"
              }
            ],
            "container-title": "IEEE Transactions on Fuzzy Systems",
            "reference-count": 0,
            "page": "625-641",
            "deposited": {
              "date-parts": [
                [
                  2013,
                  8,
                  14
                ]
              ],
              "timestamp": 1376438400000
            },
            "issue": "4",
            "title": "Reliable All-Pairs Evolving Fuzzy Classifiers",
            "type": "journal-article",
            "DOI": "10.1109/tfuzz.2012.2226892",
            "ISSN": [
              "1063-6706",
              "1941-0034"
            ],
            "URL": "http://dx.doi.org/10.1109/tfuzz.2012.2226892",
            "source": "CrossRef",
            "publisher": "Institute of Electrical & Electronics Engineers (IEEE)",
            "indexed": {
              "date-parts": [
                [
                  2014,
                  9,
                  21
                ]
              ],
              "timestamp": 1411301462763
            },
            "volume": "21",
            "member": "http://id.crossref.org/member/263",
            "page-first": "625"
          }
        </script>
      </div>
    </header>
    <!-- this lives in db paper.body -->
    <main>
      <h1 id="h1">A heading</h1>
      <p id="p1">
        Some <em id="em13">annotated</em> content (see <cite id="bibref13" typeof="bibref" data-rid="bib13">Doe, 2010</cite> and <cite id="figref9" typeof="figref" data-rid="fig1">Figure 1</cite>).
      </p>
      <h1 id="h2">Top level</h1>
      <!-- include external resource (figure) -->
      <div typeof="figinc" data-rid="fig1"></div>
    </main>
  </body>
</html>
```

Exposing the Tahi Article as an HTML document would allow somebody to edit the whole document by hand in a text editor. However, we have to be aware that importing a modified Tahi-Source-HTML file is a destructive operation. If we want to allow it, the user must be warned that all contents in the database will be overwritten with the contents of the HTML file.

# Use cases

## Different update strategies

We have to deal with two categories of document manipulations:

- **Individual field writes** : For instance update document title or body: HTML fragment is sent to the server and paper.title or paper.body is set.

- **Full document writes:** Complete Tahi Source HTML file is written: HTML is validated and verified. If successful individual fields are extracted from the file and stored in the corresponding db records. Old contents are removed (also figures and bibliographic entries) and replaced with the new contents. 

iHat for instance would use the full document write API for the initial import of a Word document. However, we have to be aware that there can only be one source of information. E.g. when after the import the author would make fixes in the Tahi Editor, he would loose all changes when he does another import of the Word document.

## Maintaining a stand-alone version of the editor

This is very important to make editor development efficient. A stand-alone version of the editor could be implemented against a simplified backend that just deals with reading and saving the Tahi-Source HTML, instead of decomposing the information into separate database tables. This allows editor development to be isolated from the Tahi platform, which has a very complex setup that is not suitable for editor development.

There's also potential in offering the Tahi Editor as a stand-alone app, so people can use it outside of the Tahi platform.
