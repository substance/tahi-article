# The Tahi Article Specification



## Persistence Level

Manuscript body (`Paper.body`)

```html
  <h1 id="h1">A heading</h1>
  <p id="p1">
    Some <em id="em13">annotated</em> content (see <cite id="bibref13" typeof="bibref" data-rid="bib13">Doe, 2010</cite> and <cite id="figref9" typeof="figref" data-rid="fig1">Figure 1</cite>).
  </p>
  <h1 id="h2">Top level</h1>
  <!-- include external resource (figure) -->
  <div typeof="figinc" data-rid="fig1"></div>
```

Title (`Paper.title`)

```html
The <em>TAHI</em> Article Format
```

Abtract (`Paper.abstract`)

```html
Article abstract that can be <strong>annotated</strong>
```


## Exchange Level

Possible self-contained representation in JSON

```json
{
  "id": "10",
  "title": "The <em>TAHI</em> Article Format",
  "abstract": "Article abstract that can be <strong>annotated</strong>",
  "body": "<h1 id=\"h1\">A heading</h1>....",
  "resources": {
    "fig1": {
      "type": "fig",
      "caption": "annotated <em>caption</em>"
    }
  }
}
```

Alternative as a combined HTML file (can be used as a snapshot for archiving)

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
    
    <bib id="bib1" bib-type="journal-article">

    <!-- this lives in db paper.main -->
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
