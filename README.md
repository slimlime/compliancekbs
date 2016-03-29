# compliancekbs
Compliance Knowledge Base Service for Security Controls Compliance Server

API server
----------

The API server requires python3 and some dependencies:

	sudo apt-get install sqlite3 wkhtmltopdf
    pip3 install -r requirements.txt

(`wkhtmltopdf` is for generating thumbnails of Markdown documents.)

Then start using:

    python3 server.py

Or in production:

	sudo PORT=80 python3 server.py

The server logs queries to an sqlite database. To get the log, run:

	sqlite3 -csv access_log.db "select * from query_log" > access_log.csv

The columns are the date/time of the query (in UTC), the user's query, and a space-separated list of document IDs that were returned by the query (in the order in which they were returned).


YAML schema
-----------

### documnet

Each YAML file describes a document. Documents have the following fields:

`id`: An identifier for this document unique within the GovReady Knowledge Base.

`title`: The display title of the document.

`short-title`: A short form display title of the document.

`url`: The preferred URL where the document can be viewed. This is a link for humans. We recognize URLs that look like `https://www.documentcloud.org/documents/###-____.html` for displaying thumbnails from DocumentCloud.

`authoritative-url`: A link to the authoritative copy of a document, i.e. as published by the document owner. The URL should return a direct download link for a file in the format given in the `format` document property. Markdown-formatted documents should have this URL set to the address that fetches the raw Markdown content (i.e. under `https://raw.githubusercontent.com`) so that the application can fetch the document to generate context and thumbnails.

`doi`: The "DOI" if the document has been assigned one (e.g. `doi:10.6028/NIST.SP.800-37r1`).

`format`: If the document is not in Document Cloud, the document's format. Can be `markdown`.

`terms`: An array of one or more terms found in the document (see below).

### term

A term is a phrase that appears in a document. It has the following attributes:

`term`: The text as it appears in the document.

`page`: The primary page on which it appears in the document (e.g. where it is defined, if applicable).

`purpose`: This field has the value `definition` if the term is defined within this document (and if  `page` is specified, then on that page).

`defined-by`: A `term-reference` (see below) to where this term is defined.

`same-as`: A `term-reference` (see below) to another term that has the same meaning.

### term-reference

A term-reference is a way of locating a term within a document. A term-reference has the following attributes:

`document`: The identifier (`id`) of the document where the referenced term occurs. If this attribute is missing, then a term is being referenced in the same document that the YAML file is describing --- i.e. to `same-as` two terms that appear in the same document.

`term`: The text of the term as it appears in that document. If omitted in a parent `term`, the term appears with the same words in the referenced document.
