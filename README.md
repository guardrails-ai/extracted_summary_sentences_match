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
$ guardrails hub install hub://guardrails/extracted_summary_sentences_match
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

**`__init__(self, on_fail="noop")`**
<ul>

Initializes a new instance of the Validator class.

**Parameters:**

- **`threshold`** _(float)_: The minimum cosine similarity to be considered similar. Default to 0.7.
- **`on_fail`** *(str, Callable):* The policy to enact when a validator fails. If `str`, must be one of `reask`, `fix`, `filter`, `refrain`, `noop`, `exception` or `fix_reask`. Otherwise, must be a function that is called when the validator fails.

</ul>

<br>

**`__call__(self, value, metadata={}) → ValidationOutcome`**

<ul>

Validates the given `value` using the rules defined in this validator, relying on the `metadata` provided to customize the validation process. This method is automatically invoked by `guard.parse(...)`, ensuring the validation logic is applied to the input data.

Note:

1. This method should not be called directly by the user. Instead, invoke `guard.parse(...)` where this method will be called internally for each associated Validator.
2. When invoking `guard.parse(...)`, ensure to pass the appropriate `metadata` dictionary that includes keys and values required by this validator. If `guard` is associated with multiple validators, combine all necessary metadata into a single dictionary.

**Parameters:**

- **`value`** *(Any):* The input value to validate.
- **`metadata`** *(dict):* A dictionary containing metadata required for validation. Keys and values must match the expectations of this validator.
    
    
    | Key | Type | Description | Default |
    | --- | --- | --- | --- |
    | `filepaths` | list[str] | A list of strings that specifies the filepaths for any documents that should be used for asserting the summary's similarity. | N/A |
    | `document_store` | Optional[DocumentStoreBase] | The document store to use during validation. Defaults to EphemeralDocumentStore. | None |
    | `vector_db` | Optional[VectorDBBase] | A vector database to use for embeddings. Defaults to Faiss. | None |
    | `embedding_model` | Optional[EmbeddingBase] | The embeddig model to use. Defaults to OpenAIEmbedding. | None |

</ul>

