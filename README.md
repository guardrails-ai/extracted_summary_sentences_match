## Overview

| Developed by | Guardrails AI |
| --- | --- |
| Date of development | Feb 15, 2024 |
| Validator type | Summarization |
| Blog |  |
| License | Apache 2 |
| Input/Output | Output |

## Description

This validator checks that sentences in a generated summary match the original text by performing a cosine similarity in the embedding space.

### Intended use

This validator is only useful when performing summarization. If the summary is correct but is an abstractive summary, this validator will give false negatives.

### Resources required

- Dependencies: `faiss`, `openai`

## Installation

```bash
$ gudardrails hub install hub://guardrails/extracted_summary_sentences_match
```

## Usage Examples

### Validating string output via Python

In this example, we apply the validator to a string output generated by an LLM.

```python
# Import Guard and Validator
from guardrails.hub import ExtractedSummarySentencesMatch
from guardrails import Guard

# Initialize Validator
val = ExtractedSummarySentencesMatch(
    threshold=0.8,
    filepaths="/path/to/original/documents"
)

# Setup Guard
guard = Guard.from_string(validators=[val, ...])

guard("Summarized text")  # Validator passes
guard("Inaccurate summary")  # Validator fails
```

### Validating JSON output via Python

In this example, we apply the validator to a string field of a JSON output generated by an LLM.

```python
# Import Guard and Validator
from pydantic import BaseModel
from guardrails.hub import ExtractedSummarySentencesMatch
from guardrails import Guard

# Initialize Validator
val = ExtractedSummarySentencesMatch(
    threshold=0.8,
    filepaths="/path/to/original/documents"
)

# Create Pydantic BaseModel
class Summary(BaseModel):
    title: str
    summary: str = Field(
	description="Summary of article", validators=[val]
    )

# Create a Guard to check for valid Pydantic output
guard = Guard.from_pydantic(output_class=Summary)

# Run LLM output generating JSON through guard
guard.parse("""
{
    "title": "Using Guardrails Hub",
    "summary": "To use Guardrails Hub, use the hub cli to download validators."
}
""")
```

## API Reference

`__init__`

- `threshold` - The minimum cosine similarity to be considered similar. Default to 0.7.
    Other parameters: Metadata

`__call__`

- `filepaths` *List[str]* - A list of strings that specifies the filepaths for any documents that should be used for asserting the summary's similarity.
- `document_store` *DocumentStoreBase, optional* - The document store to use during validation. Defaults to EphemeralDocumentStore.
- `vector_db` *VectorDBBase, optional* - A vector database to use for embeddings. Defaults to Faiss.
- `embedding_model` *EmbeddingBase, optional* - The embeddig model to use. Defaults to OpenAIEmbedding.



