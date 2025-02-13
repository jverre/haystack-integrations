---
layout: integration
name: Azure AI Search
description: Use Azure AI Search with Haystack
authors:
    - name: deepset
      socials:
        github: deepset-ai
        twitter: deepset_ai
        linkedin: https://www.linkedin.com/company/deepset-ai/
pypi: https://pypi.org/project/azure-ai-search
repo: https://github.com/deepset-ai/haystack-core-integrations/tree/main/integrations/azure-ai-search
type: Document Store
report_issue: https://github.com/deepset-ai/haystack-core-integrations/issues
logo: /logos/azure-ai.png
version: Haystack 2.0
toc: true
---

### **Table of Contents**
- [Overview](#overview)
- [Installation](#installation)
- [Usage](#usage)

## Overview

`AzureAIDocumentStore` supports an integration of [Azure AI Search](https://learn.microsoft.com/en-us/azure/search/search-what-is-azure-search) which is an enterprise-ready search and retrieval system with [Haystack](https://haystack.deepset.ai/) by [deepset](https://www.deepset.ai).

This integration allows using search indexes in Azure AI Search as a document store to build RAG-based applications on Azure, with native LLM integrations. To retrieve data from the document store, the integration supports three types of retrieval techniques:

1. **Embedding Retrieval**: For vector-based searches.
2. **BM25 Retrieval**: Keyword retrieval utilizing the BM25 algorithm.
3. **Hybrid Retrieval**: A combination of vector and BM25 retrieval methods.

## Installation

Install the Azure AI Search integration:

```bash
pip install "azure-ai-search-haystack"
```

## Usage

To use the `AzureAISearchDocumentStore`, you need to have an active [Azure subscription](https://azure.microsoft.com/en-us/products/ai-services/ai-search) with a deployed Azure AI Search service. You need to provide a search service endpoint as an `AZURE_AI_SEARCH_ENDPOINT` and an API key as `AZURE_AI_SEARCH_API_KEY` for authentication. If the API key is not provided, the `DefaultAzureCredential` will attempt to authenticate you through the browser.

```python
from haystack_integrations.document_stores.azure_ai_search import AzureAISearchDocumentStore
from haystack import Document

document_store = AzureAISearchDocumentStore(
    metadata_fields={"version": float, "label": str},
    index_name="document-store-example",
)

documents = [
    Document(
        content="This is an introduction to using Python for data analysis.",
        meta={"version": 1.0, "label": "chapter_one"},
    ),
    Document(
        content="Learn how to use Python libraries for machine learning.",
        meta={"version": 1.5, "label": "chapter_two"},
    ),
    Document(
        content="Advanced Python techniques for data visualization.",
        meta={"version": 2.0, "label": "chapter_three"},
    ),
]
document_store.write_documents(documents)

filters = {
    "operator": "AND",
    "conditions": [
        {"field": "meta.version", "operator": ">", "value": 1.2},
        {"field": "meta.label", "operator": "in", "value": ["chapter_one", "chapter_three"]},
    ],
}

results = document_store.filter_documents(filters)
print(results)
```

You can supply all supported parameters as `index_creation_kwargs` for `SearchIndex` during the initialization of the `AzureAISearchDocumentStore` to customize index creation. Additionally, the `AzureAISearchDocumentStore` supports semantic ranking, which can be enabled by including the `SemanticSearch` configuration in index_creation_kwargs during initialization and utilizing it through one of the retrievers. For further details, refer to the [Azure AI tutorial](https://learn.microsoft.com/en-us/azure/search/search-get-started-semantic?tabs=dotnet) on this feature.

