## Details

| Developed by | Guardrails AI |
| --- | --- |
| Date of development | Feb 15, 2024 |
| Validator type | Summarization |
| Blog |  |
| License | Apache 2 |
| Input/Output | Output |

## Description

This validator checks that sentences in a generated summary match the original text by performing a cosine similarity in the embedding space.

## Example Usage Guide

### Installation

```bash
$ gudardrails hub install extracted-summary-sentences-match
```

### Initialization

```python
extractive_summary_validator = ExtractedSummarySentencesMatch(
	threshold=0.8,
	filepaths="/path/to/original/documents"
)

# Create Guard with Validator
guard = Guard.from_string(
    validators=[extractive_summary_validator, ...],
    num_reasks=2,
)
```

### Invocation

```python
guard(
    "Summarized text",
)
```

## API Ref

- `threshold` - The minimum cosine similarity to be considered similar. Default to 0.7.
    
    Other parameters: Metadata
    
- `filepaths` *List[str]* - A list of strings that specifies the filepaths for any documents that should be used for asserting the summary's similarity.
- `document_store` *DocumentStoreBase, optional* - The document store to use during validation. Defaults to EphemeralDocumentStore.
- `vector_db` *VectorDBBase, optional* - A vector database to use for embeddings. Defaults to Faiss.
- `embedding_model` *EmbeddingBase, optional* - The embeddig model to use. Defaults to OpenAIEmbedding.

## Intended use

- Primary intended uses: This validator is only useful when performing summarization.
- Out-of-scope use cases: If the summary is correct but is an abstractive summary, this validator will give false negatives.

## Expected deployment metrics

|  | CPU | GPU |
| --- | --- | --- |
| Latency |  | - |
| Memory |  | - |
| Cost |  | - |
| Expected quality |  | - |

## Resources required

- Dependencies: `thefuzz`
- Foundation model access keys: N/A
- Compute: N/A

## Validator Performance

### Evaluation Dataset

-

### Model Performance Measures

| Accuracy | - |
| --- | --- |
| F1 Score | - |

### Decision thresholds
