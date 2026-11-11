# zuhura-docs

Technical, architectural, and scientific documentation for the **Zuhura Mars Digital Twin & Scientific Intelligence Platform**.

## Structure
- `docs/index.md`: Mission overview and high-level platform introduction.
- `docs/geodesy/`: Mathematical specifications for Mars ellipsoids, latitude conversions, and Areoid elevation datums.
- `docs/architecture/`: System design diagrams, ADRs, and component interaction models.
- `docs/data/`: Planetary scientific dataset guides, PDS archives, and STAC extensions.
- `docs/ai/`: Grounded RAG architecture, tool calling guardrails, and citation policies.
- `docs/developer/`: Quickstart, setup runbooks, and testing instructions.

## Building the Documentation Locally
```bash
pip install mkdocs-material
mkdocs serve
```
Open `http://localhost:8000` to view the documentation site.
