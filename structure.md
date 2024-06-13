## Overview

The data from [regulations.gov](https://www.regulations.gov/) is organized into *dockets*.  Each docket contains all *documents* and *comments* for one proposed rule.  For each docket, document, and comment there is one JSON file containing a variety of information about the element.  The documents and comments can contain attachments (typically `.doc`, `.docx`, or `.pdf` files) that are linked as a field in the JSON file.

All elements are identified by an ID. A docket id is made up of:

* agency abbreviation
* year of docket
* docket number

Document IDs and comment IDs are made up of the docket ID followed by a unique number.

## Structure of the Data

The data is organized by agency and docket.  Within each docket, the binary data (attachments) are stored separately from the text data so that users can easily download only the text data.

The structure separates the results from multiple text extraction tools.

```
data
└── <agency>
    └── <docket id>
        ├── binary-<docket id>
        │   ├── comments_attachments
        │   │   ├── <comment id>_attachement_<counter>.<extension>
        │   │   └── ...
        │   ├── documents_attachments
        │   │   ├── <document id>_attachement_<counter>.<extension>
        │   │   └── ...
        └── text-<docket id>
            ├── comments
            │   ├── <comment id>.json
            │   └── ...
            ├── comments_extracted_text
            │   ├── <tool name>
            │   |   ├── <comment id>_attachment_<counter>_extracted.txt
            │   |   └── ...
            |   └─ ... <other tools>
            ├── docket
            │   ├── <docket id>.json
            |   └── ...
            ├── documents
            │   ├── <document id>.json
            │   ├── <document id>_content.htm
            │   └── ...
            └── documents_extracted_text
                ├── <tool name>
                |   ├── <document id>_content_extracted.txt
                |   └── ...
                └─ ... <other tools>
```                    

## Example

The `USTR` contains a docket id `USTR-2015-0010` that holds 1 docket, 4 documents, and 4 comments.  Each of the comments has an attachment, and each of the documents have one or more attachments.  The tool `pikepdf` was used to extract text from these attachments.

This data would be stored in the structure as follows:

```
USTR
└── USTR-2015-0010
    ├── binary-USTR-2015-0010
    │   ├── comments_attachments
    │   │   ├── USTR-2015-0010-0002_attachment_1.pdf
    │   │   ├── USTR-2015-0010-0003_attachment_1.pdf
    │   │   ├── USTR-2015-0010-0004_attachment_1.pdf
    │   │   └── USTR-2015-0010-0005_attachment_1.pdf
    │   └── documents_attachments
    │       ├── USTR-2015-0010-0001_content.pdf
    │       ├── USTR-2015-0010-0015_content.pdf
    │       ├── USTR-2015-0010-0016_content.doc
    │       ├── USTR-2015-0010-0016_content.pdf
    │       ├── USTR-2015-0010-0017_content.doc
    │       └── USTR-2015-0010-0017_content.pdf
    └── text-USTR-2015-0010
        ├── comments
        │   ├── USTR-2015-0010-0002.json
        │   ├── USTR-2015-0010-0003.json
        │   ├── USTR-2015-0010-0004.json
        │   └── USTR-2015-0010-0005.json
        ├── comments_extracted_text
        │   └── pikepdf
        │       ├── USTR-2015-0010-0002_attachment_1_extracted.txt
        │       ├── USTR-2015-0010-0003_attachment_1_extracted.txt
        │       ├── USTR-2015-0010-0004_attachment_1_extracted.txt
        │       └── USTR-2015-0010-0005_attachment_1_extracted.txt
        ├── docket
        │   └── USTR-2015-0010.json
        ├── documents
        │   ├── USTR-2015-0010-0001.json
        │   ├── USTR-2015-0010-0001_content.htm
        │   ├── USTR-2015-0010-0015.json
        │   ├── USTR-2015-0010-0016.json
        │   └── USTR-2015-0010-0017.json
        └── documents_extracted_text
            └── pikepdf
                ├── USTR-2015-0010-0015_content_extracted.txt
                ├── USTR-2015-0010-0016_content_extracted.txt
                └── USTR-2015-0010-0017_content_extracted.txt
```

## Explanation

* At the root level, there is a folder, `USTR` for the agency.
  * In the agency folder there is a folder for the docket ID, `USTR-2015-0010`.
    * In the docket folder there are two subfolders to separate the binary data and text data called `binary-USTR-2015-0010` and `text-USTR-2015-0010`.
    * The `binary-USTR-2015-0010` folder contains two subdirectories, `comments_attachments`, and `document_attachments` to hold the attachments for comments and documents, respectively.
      * The `comments_attachments` folder contains each attachment file named using the comment id followed by the attachment number, such as `USTR-2015-0010-0002_attachment_1.pdf`.
      * The `documents_attachments` folder contains each attachment file named using the document id followed by the word `content`, such as `USTR-2015-0010-0001_content.pdf` and `USTR-2015-0010-0016_content.doc`.
    * The `text-USTR-2015-0010` folder contains five subdirectories: `docket`, `documents`, `comments`, `comments_extracted_text`, and `documents_extracted_text`.
      * The `comments` folder contains a JSON file for each comment, named with the comment ID for each comment, such as `USTR-2015-0010-0002.json`.
      * The `comments_extracted_text` folder contains a subdirectory for each extraction tool used. In this example, only the tool  `pikepdf` was used.
          * The `pikepdf` directory contains one text file for each attachment with extracted text.  These files are named with the comment id, attachment number, and the word `extracted` such as `USTR-2015-0010-0002_attachment_1_extracted.txt`
      * The `docket` folder contains th JSON file for the docket in a file named with the docket ID, such `USTR-2015-0010.json`.
      * The `documents` folder contains one JSON for each document along with the HTM file containing the docket text.  Both files are named using the document ID such as `USTR-2015-0010-0001.json` and `USTR-2015-0010-0001_content.htm`.
      * The `documents_extracted_text` folder contains a subdirectory for each extraction tool used. In this example, only the tool  `pikepdf` was used.
          * The `pikepdf` directory contains one text file for each attachment with extracted text.  These files are named with the document id, document number, and the word `extracted` such as `USTR-2015-0010-0001_content_extracted.txt`
