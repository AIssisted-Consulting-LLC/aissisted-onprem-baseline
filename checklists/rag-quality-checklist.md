# RAG Quality Checklist

Use this before blaming the model.

## Corpus quality

- [ ] Sources are current
- [ ] Duplicate documents are removed or tagged
- [ ] Superseded policies are archived or excluded
- [ ] File names and source paths are meaningful
- [ ] Tables survive extraction well enough to answer real questions
- [ ] Images with critical meaning have captions or alt text in the corpus

## Chunking and indexing

- [ ] Chunk size fits the document type
- [ ] Overlap is intentional, not cargo cult
- [ ] Headings stay with their content
- [ ] Metadata includes source, owner, date, and document type
- [ ] Indexing failures are logged
- [ ] Re-index path is documented

## Retrieval behavior

- [ ] Top-k is tested, not guessed
- [ ] Filters work for department, date, or document class when needed
- [ ] Retrieval returns the current document over the obsolete one
- [ ] Empty retrieval is handled explicitly
- [ ] Low-confidence retrieval is visible to the operator

## Answer quality

- [ ] Answers cite source documents when required
- [ ] The agent says when it does not know
- [ ] The agent does not blend conflicting versions into one confident lie
- [ ] The answer length fits the operator task
- [ ] Critical instructions are reproduced accurately

## Failure drills

- [ ] Test with a document that should be found easily
- [ ] Test with a revoked or outdated document
- [ ] Test with a scanned PDF
- [ ] Test with a table-heavy file
- [ ] Test with near-duplicate documents
- [ ] Test with a question that should return no answer

## If retrieval is weak, check these first

1. Extraction quality
2. Metadata quality
3. Duplicate documents
4. Chunk boundaries
5. Wrong filters
6. Stale index
7. Query rewrite doing something dumb
