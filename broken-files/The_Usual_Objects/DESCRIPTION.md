## PDF

### 1. What is a PDF File?
A **PDF (Portable Document Format)** is not a plain text file—it is a binary-safe container built around a **structured storage system** of independent **objects**. Every page, font, layout rule, and compressed text stream is stored as a separate numbered object that links to other objects.



### 2. File Structure (4 Core Parts)
A standard PDF file is organized into four sequential sections:

1. **Header:** Identifies the file magic number and specification version (e.g., `%PDF-1.4`).
2. **Body:** Holds all the **indirect objects** that make up the document's content, fonts, and page hierarchy.
3. **Cross-Reference Table (`xref`):** An index mapping each object ID to its exact **byte offset** from the start of the file so readers can jump directly to any object without reading the whole file linearly.
4. **Trailer:** Located at the very bottom (`trailer`, `startxref`, `%%EOF`). PDF parsers start reading from the **end** of the file to find the `xref` offset and the entry point into the object tree.


### 3. What is an Object?
Objects are wrapped between `obj` and `endobj` markers and prefixed by two numbers: an **Object Number** and a **Generation Number** (e.g., `12 0 obj`).

Inside, objects use basic PDF data types:
* **Dictionaries (`<< /Key Value >>`):** Key-value maps that define object roles and attributes (such as `/Type`, `/Font`, `/Filter`, or `/Length`).
* **Arrays (`[ ... ]`):** Ordered lists of values or object references.
* **Streams (`stream ... endstream`):** Dictionaries followed by raw or compressed byte sequences (such as `FlateDecode` zlib-compressed page drawing instructions).



### 4. How Objects Connect
Objects reference one another using **Indirect References** formatted as **`<ObjectNumber> <GenerationNumber> R`** (for example, `12 0 R`).

Together, these references form a **directed tree**:
* The **Trailer** points to the root document object.
* That root object points to the **page tree** node.
* The page tree points to individual **page** objects.
* Each page points to its **resources** (fonts) and its **content stream** (the actual rendered text and graphics).

If those references point to invalid or missing object numbers, the parser cannot traverse the tree to render the document.

> **Hex Editor Note:** PDF structure keywords, object IDs, and indirect references are stored as plain **ASCII text**, not raw binary integers. If you patch numbers in a hex editor, use their ASCII hex codes (for example, `'0'` is `0x30` and `'9'` is `0x39`, **not** `0x00` or `0x09`), and keep the byte count unchanged so the `xref` offsets remain valid.




