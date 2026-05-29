Advocate Smart Search — chunked data version

Files:
- index.html
- advocatedata1.js: licenceNo 1–5000
- advocatedata2.js: licenceNo 5001–10000
- advocatedata3.js: licenceNo 10001–15000
- etc.

How it works:
- Keep all advocatedata*.js files in the same folder as index.html.
- Open index.html.
- The webpage UI/display remains the same, but data is loaded from chunk files instead of one huge inline DATA array.

Data file format:
registerAdvocateDataChunk(1, [
  {"licenceNo":"1","name":"...","englishName":"...","address":"...","sex":"...","type":"...","issueDate":"..."}
]);

Generated from uploaded table:
- Total records: 21529
- Chunk files generated: 5
