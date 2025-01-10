# GraphRAG: Building Knowledge Graphs with Advanced Entity Resolution

## Overview

GraphRAG is a framework for constructing robust knowledge graphs from unstructured and semi-structured data sources. It integrates advanced entity resolution techniques with retrieval-augmented generation to create rich, contextual knowledge representations.

## Acknowledgments

This project builds upon work from the [Connected Data London 2024 Entity Resolved Knowledge Graphs masterclass](https://github.com/DerwenAI/cdl2024_masterclass/blob/main/README.md), with contributions from:

- Paco Nathan (Senzing) - Knowledge Graph Architecture
- Clair Sullivan - Graph Analytics
- Louis Guitton - Entity Resolution
- Jeff Butcher - System Integration
- Michael Dockter - Implementation

## Core Architecture

GraphRAG implements a layered approach to knowledge graph construction:

### Ingestion

```mermaid
graph TD
    %% Database shapes with consistent styling
    SDS[(Structured<br/>Data Sources)]
    UDS[(Unstructured<br/>Data Sources)]
    LG[(lexical graph)]
    SG[(semantic graph)]
    VD[(vector database)]

    %% Flow from structured data
    SDS -->|PII features| ER[entity resolution]
    SDS -->|data records| SG
    SG -->|PII updates| ER
    ER -->|semantic overlay| SG

    %% Schema and ontology
    ONT[schema, ontology, taxonomy,<br/>controlled vocabularies, etc.]
    ONT --> SG

    %% Flow from unstructured data
    UDS --> K[text chunking<br/>function]
    K --> NLP[NLP parse]
    K --> EM[embedding model]
    NLP --> E[NER, RE]
    E --> LG
    LG --> EL[entity linking]
    EL <--> SG

    %% Vector elements connections
    EM --> VD
    VD -.->|capture source chunk<br/>WITHIN references| SG

    %% Thesaurus connection
    ER -.->T[thesaurus]
    T --> EL

    %% Styling classes
    classDef dataSource fill:#f4f4f4,stroke:#666,stroke-width:2px;
    classDef storage fill:#e6f3ff,stroke:#4a90e2,stroke-width:2px;
    classDef embedding fill:#fff3e6,stroke:#f5a623,stroke-width:2px;
    classDef lexical fill:#f0e6ff,stroke:#4a90e2,stroke-width:2px;
    classDef semantic fill:#f0e6ff,stroke:#9013fe,stroke-width:2px;
    classDef reference fill:#e6ffe6,stroke:#417505,stroke-width:2px;

    %% Apply styles by layer/type
    class SDS,UDS dataSource;
    class SG,VD storage;
    class EM embedding;
    class LG lexical;
    class SG semantic;
    class ONT,T reference;
```

### Inference

```mermaid
graph LR
    %% Define database and special shapes
    P[prompt]
    SG[(semantic graph)]
    VD[(vector database)]
    LLM[LLM]
    Z[response]
    
    %% Main flow paths
    P --> Q[generated query]
    P --> EM[embedding model]
    
    %% Upper path through graph elements
    Q --> SG
    SG --> W[semantic<br/>random walk]
    T[thesaurus] --> W
    W --> GA[graph analytics]
    
    %% Lower path through vector elements
    EM --> SS[vector<br/>similarity search]
    SS --> VD
    
    %% Node embeddings and chunk references
    SG -.-|chunk references| VD
    SS -->|node embeddings| SG
    
    %% Final convergence
    GA --> RI[ranked index]
    VD --> RI
    RI --> LLM
    LLM --> Z

    %% Styling classes
    classDef dataSource fill:#f4f4f4,stroke:#666,stroke-width:2px;
    classDef storage fill:#e6f3ff,stroke:#4a90e2,stroke-width:2px;
    classDef process fill:#fff3e6,stroke:#f5a623,stroke-width:2px;
    classDef lexical fill:#f0e6ff,stroke:#4a90e2,stroke-width:2px;
    classDef semantic fill:#f0e6ff,stroke:#9013fe,stroke-width:2px;
    classDef reference fill:#e6ffe6,stroke:#417505,stroke-width:2px;

    %% Apply styles by layer/type
    class SDS,UDS dataSource;
    class SG,VD storage;
    class ER,NLP,E,K,EM process;
    class LG lexical;
    class SG semantic;
    class ONT,T reference;
    class EL process;
```

## Key Components

### 1. Data Ingestion
- BeautifulSoup for web scraping
- Configurable chunking (1024 tokens default)
- Unicode normalization and text cleaning

### 2. Entity Processing
- SpaCy pipeline with GLiNER and GLiREL integration
- Custom NER labels for domain-specific entities
- Relationship extraction with confidence scoring

### 3. Graph Construction
- NetworkX for graph operations
- TextRank-based entity importance scoring
- Semantic overlay abstraction

### 4. Vector Operations
- BAAI/bge-small-en-v1.5 embeddings
- LanceDB for vector storage
- Word2Vec for entity embeddings

### 5. Visualization
- PyVis for interactive graph visualization
- Dynamic node sizing based on entity importance
- Physics-based layout system

## Technical Features

### Entity Types
- Person
- Organization
- Publication
- Technology
- Location
- Event
- Research
- University
- Country
- City

### Relationship Types
- works_at
- developed_by
- authored_by
- headquartered_in
- used_in
- studied_at

### Performance Optimizations
- Chunked processing for large documents
- Efficient entity deduplication
- Memory-aware graph construction

## Installation

1. Environment Setup:
```bash
# Python 3.10+ required
pip install -r requirements.txt
```

2. Start Prefect server:
```bash
prefect server start
```

3. Run the demo:
```bash
python graphrag_demo.py
```

## Implementation Details

The system uses a multi-stage pipeline:

1. **Data Preparation**
   - Text chunking with overlap
   - Unicode normalization
   - Stop word filtering

2. **Entity Extraction**
   - NER with GLiNER
   - Relationship extraction with GLiREL
   - Co-occurrence analysis

3. **Graph Construction**
   - Lexical graph building
   - TextRank scoring
   - Semantic overlay creation

4. **Data Persistence**
   - JSON graph serialization
   - GraphML export
   - Vector database storage

## Advanced Features

### Entity Resolution
- Cross-reference resolution
- Contextual entity linking
- Confidence scoring

### Graph Analytics
- PageRank-inspired entity ranking
- Co-occurrence analysis
- Relationship probability scoring

### Data Quality
- Source attribution
- Confidence metrics
- Entity validation

## Visualization

The system generates an interactive HTML visualization with:
- Color-coded entity types
- Relationship labels
- Size-based entity importance
- Interactive physics simulation

## Future Development

1. Real-time graph updates
2. Enhanced entity resolution
3. Multi-source integration
4. Advanced query capabilities
5. Automated validation workflows

## Contributing

We welcome contributions! Please see our contributing guidelines for details.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

Refer to the source repository for this fork for additional details:  https://github.com/DerwenAI/strwythura