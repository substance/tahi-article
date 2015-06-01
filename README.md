# The Tahi Article Specification

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
  <h1 id="h2">Top level</h1>
  <!-- include external resource (figure) -->
  <div typeof="figinc" data-rid="fig1"></div>
```

Allowed tags: `<p>`, `<h1>`, `<h2>`, `<h3>`, `<em>`, `<strong>`, `<div class="figinc">`

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
        <div property="authors">
          <div typeof="author">
            <div property="surname">Doe</div>
            <div property="given_names">John</div>
          </div>
        </div>
        <div property="year">2010</div>
        ...
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

iHat for instance would use the full document write API for the initial import of a Word document. Howeverm we have to be aware that there can only be one source of information. E.g. when after the import the author would make fixes in the Tahi Editor, he would loose all changes when he does another import of the Word document.

## Maintaining a stand-alone version of the editor

This is very important to make editor development efficient. A stand-alone version of the editor could be implemented against a simplified backend that just deals with reading and saving the Tahi-Source HTML, instead of decomposing the information into separate database tables. This allows editor development to be isolated from the Tahi platform, which has a very complex setup that is not suitable for editor development.

There's also potential in offering the Tahi Editor as a stand-alone app, so people can use it outside of the Tahi platform.