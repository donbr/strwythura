# GraphRAG README

## Citations: giving credit where credit is due...

Inspired by the great work done by multiple individuals who created the [Connected Data London 2024: Entity Resolved Knowledge Graphs](https://github.com/DerwenAI/cdl2024_masterclass/blob/main/README.md) masterclass I created this document to highlight areas that I found .

- Paco Nathan https://senzing.com/consult-entity-resolution-paco/
- Clair Sullivan https://clairsullivan.com/
- Louis Guitton https://guitton.co/
- Jeff Butcher https://github.com/jbutcher21
- Michael Dockter https://github.com/docktermj

This current repository is a fork of one of four repos that make up the masterclass.

## Sequence Diagram

```mermaid
sequenceDiagram
    participant Main as Main Script
    participant ConstructKG as construct_kg Flow
    participant InitNLP as init_nlp Task
    participant ScrapeHTML as scrape_html Task
    participant MakeChunk as make_chunk Task
    participant ParseText as parse_text Task
    participant MakeEntity as make_entity Task
    participant ExtractEntity as extract_entity Task
    participant ExtractRelations as extract_relations Task
    participant ConnectEntities as connect_entities Task
    participant RunTextRank as run_textrank Task
    participant AbstractOverlay as abstract_overlay Task
    participant GenPyvis as gen_pyvis Task

    Main->>ConstructKG: Start construct_kg flow
    ConstructKG->>InitNLP: Initialize NLP pipeline
    InitNLP-->>ConstructKG: Return NLP object

    loop For each URL in url_list
        ConstructKG->>ScrapeHTML: Scrape HTML content
        ScrapeHTML->>MakeChunk: Create text chunks
        MakeChunk-->>ScrapeHTML: Return chunk list
        ScrapeHTML-->>ConstructKG: Return chunk list

        loop For each chunk in chunk_list
            ConstructKG->>ParseText: Parse text and build lex_graph
            ParseText->>MakeEntity: Create entities from spans
            MakeEntity-->>ParseText: Return entity
            ParseText->>ExtractEntity: Extract and add entities to lex_graph
            ExtractEntity-->>ParseText: Entity added to graph
            ParseText->>ExtractRelations: Extract relations between entities
            ExtractRelations-->>ParseText: Relations added to graph
            ParseText->>ConnectEntities: Connect co-occurring entities
            ConnectEntities-->>ParseText: Connections added to graph
            ParseText-->>ConstructKG: Return parsed doc
        end

        ConstructKG->>RunTextRank: Run TextRank on lex_graph
        RunTextRank-->>ConstructKG: Return ranked entities
        ConstructKG->>AbstractOverlay: Overlay semantic graph
        AbstractOverlay-->>ConstructKG: Overlay completed
    end

    ConstructKG->>GenPyvis: Generate Pyvis visualization
    GenPyvis-->>ConstructKG: Visualization saved
    ConstructKG-->>Main: Flow completed
```

## Run the code

1. setup local Python environment and install Python dependencies

   - I used Python 3.11, but 3.10 should work as well

    ```bash
    pip install -r requirements.txt
    ```

2. Start the local Prefect server

   - follow the [self-hosted instructions](https://docs.prefect.io/v3/get-started/quickstart#connect-to-a-prefect-api) to launch the `Prefect UI`

    ```python
    prefect server start
    ```

3. run the `graphrag_demo.py` script

    ```python
    python graphrag_demo.py
    ```
