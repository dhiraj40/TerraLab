# TerraLab

### Your data platform. On your machine.

**TerraLab is a local-first Data Engineering Platform** designed to help students, developers, and data engineers learn, build, experiment with, and test modern data engineering workflows on their own computers.

The goal is to bring compute, catalogs, storage, and modern table formats together in one developer-friendly environment—without making a cloud subscription or complicated manual setup a prerequisite.

> **Build. Learn. Experiment. Locally.**

> **Project status:** Early-stage project. The architecture and roadmap below describe the intended direction. Not every capability is implemented or production-ready yet.

---

## Why TerraLab?

Modern data engineering projects often involve several moving parts:

- Compute engines such as Apache Spark.
- Catalog and metadata services such as Apache Hive Metastore.
- Storage systems such as local filesystems and cloud object storage.
- Table formats such as Parquet, Delta Lake, and Apache Iceberg.
- Development interfaces such as Python, SQL, command-line tools, and web applications.

Setting up these components individually can take time and make experimentation expensive or inaccessible. Managed cloud platforms are valuable, especially in production, but they are not always necessary for learning, prototyping, or local development.

TerraLab aims to reduce that friction by providing a unified environment that runs primarily on your own hardware.

## Who is it for?

| Audience | What TerraLab aims to help with |
|---|---|
| Students and aspiring data engineers | Learn Spark, SQL, ETL, catalogs, storage, and lakehouse concepts through hands-on projects. |
| Developers and data engineers | Prototype pipelines, test table formats, validate integrations, and experiment with data architectures locally. |

## Goals

- **Local-first compute:** Develop and run workloads on your own machine.
- **Real technologies:** Use established data engineering projects and open table formats.
- **Simpler setup:** Reduce the effort required to configure and connect platform components.
- **Multiple catalogs:** Work with independent catalog environments.
- **Flexible storage:** Use local storage or connect to cloud object storage when needed.
- **Developer-friendly workflows:** Manage the environment through a CLI, Python SDK, and Web UI as the project evolves.
- **Cloud-compatible development:** Build and test locally while learning concepts that transfer to cloud environments.

## Planned technology stack

The following technologies are part of the proposed architecture. Exact versions and compatibility combinations will be pinned and tested as implementation progresses.

| Area | Planned technology |
|---|---|
| Compute engine | Apache Spark / PySpark |
| Catalog service | Apache Hive Metastore |
| Metadata database | PostgreSQL |
| Platform control database | PostgreSQL |
| Table formats | Apache Parquet, Delta Lake, Apache Iceberg |
| Local storage | Local filesystem |
| Cloud storage | AWS S3, Azure Data Lake Storage, Google Cloud Storage |
| S3-compatible local testing | MinIO (optional) |
| Container runtime | Docker / Docker Compose |
| API | FastAPI |
| Python interface | Python SDK |
| Web interface | React |
| Configuration | YAML with validated configuration models |

TerraLab coordinates these components; it does not aim to reimplement Spark, Hive, Delta Lake, or Iceberg.

## Architecture overview

```mermaid
flowchart TB
    U[TerraLab users]
    U --> CLI[CLI]
    U --> SDK[Python SDK]
    U --> WEB[Web UI]

    CLI --> API[TerraLab API / Control Plane]
    SDK --> API
    WEB --> API

    API --> CM[Catalog Manager]
    API --> COMP[Compute Manager]
    API --> SM[Storage Manager]
    API --> CFG[Configuration & Lifecycle Manager]

    CM --> HMS[Hive Metastore service(s)]
    HMS --> HDB[(Hive metadata databases)]
    CFG --> CDB[(TerraLab control database)]

    COMP --> NATIVE[Native Spark runtime]
    COMP --> DOCKER[Docker Spark runtime]

    NATIVE --> DATA[Local or cloud storage]
    DOCKER --> DATA
    DATA --> FORMATS[Parquet / Delta Lake / Iceberg]
```

### Architecture principles

- **Separate control metadata from data metadata.** TerraLab's control database stores platform configuration and registered resources. Hive Metastore stores catalog metadata for databases and tables.
- **Keep data in storage.** PostgreSQL is not the data warehouse. Table data remains in local filesystems or object storage.
- **Use native implementations.** Spark and the table-format libraries handle processing and table operations.
- **Keep modules loosely coupled.** Catalog, compute, storage, and format integrations should have clear interfaces.
- **Make runtime compatibility explicit.** Spark, Java, Hive, Delta, Iceberg, and cloud connector versions must be tested together rather than assumed to be interchangeable.

## Planned capabilities

| Capability | Direction |
|---|---|
| Native local Spark execution | Planned |
| Docker-based Spark execution | Planned |
| Multiple Hive Metastore catalogs | Planned |
| Local filesystem storage | Planned |
| Parquet tables | Planned |
| Delta Lake tables | Planned |
| Apache Iceberg tables | Planned |
| AWS S3 connectivity | Planned |
| Azure ADLS connectivity | Planned |
| Google Cloud Storage connectivity | Planned |
| CLI | Planned |
| Python SDK | Planned |
| Web UI | Planned |

> Update this table as features are implemented. A planned feature should not be described as stable until it has working tests and documentation.

## Roadmap

### 0.1 — Local foundation

- [ ] Establish repository structure and development standards.
- [ ] Provide Docker Compose infrastructure for PostgreSQL and Hive Metastore.
- [ ] Implement TerraLab control database and configuration.
- [ ] Register and manage one or more catalog endpoints.
- [ ] Support native local PySpark execution.
- [ ] Support local filesystem storage and Parquet workflows.
- [ ] Provide initial CLI commands for setup, start, stop, status, and catalog management.
- [ ] Add health checks, logs, and persistence tests.
- [ ] Document a clean installation and first-run experience.

### 0.2 — Lakehouse and storage integrations

- [ ] Add Delta Lake integration and compatibility tests.
- [ ] Add Apache Iceberg integration and compatibility tests.
- [ ] Add Docker-based Spark execution.
- [ ] Improve multi-catalog workflows.
- [ ] Add cloud storage integrations for S3, ADLS, and GCS.
- [ ] Add storage connection validation and credential handling.

### 0.3 — Developer experience

- [ ] Build the FastAPI service.
- [ ] Publish the Python SDK.
- [ ] Build the Web UI.
- [ ] Add catalog and table exploration.
- [ ] Add compute session and job monitoring.
- [ ] Improve logs, diagnostics, and developer documentation.

Roadmap items are goals, not release commitments. Scope and priorities may change as the project is developed and tested.

## Getting started

TerraLab is under active initial development. Installation instructions will be added when the first reproducible setup is available.

The intended first-run experience is:

```text
install TerraLab
start TerraLab
create or select a catalog
start a compute session
run a PySpark job
read and write a local table
```

For now, see the repository issues and project roadmap for implementation progress.

## Example of the intended developer experience

The following is an illustrative API concept, not a currently supported interface:

```python
from terralab import TerraLab

platform = TerraLab.connect()

catalog = platform.catalog("dev")

spark = platform.compute.create_session(
    mode="native",
    catalog="dev",
)

df = spark.range(10)

df.write.mode("overwrite").parquet(
    "./warehouse/sample_table"
)

spark.stop()
```

The actual API will be documented once it is implemented.

## Project principles

1. **Local first, cloud optional.**
2. **Open technologies and flexible architecture.**
3. **Simple setup, practical development.**
4. **Learn by building, not just reading.**
5. **Transparent compatibility and honest feature status.**

## Security

- Do not commit cloud credentials, passwords, tokens, private keys, or local secret files.
- Prefer environment variables, cloud identity mechanisms, or supported secret stores for credentials.
- Keep local configuration and generated data out of version control unless explicitly intended.
- Report security issues privately to the maintainers rather than publishing exploitable details in an issue.

## Contributing

Contributions, ideas, bug reports, and documentation improvements are welcome.

Before opening a pull request:

1. Check existing issues and discussions.
2. Describe the problem or proposed change.
3. Add or update tests for behavior changes.
4. Update documentation when interfaces or setup steps change.
5. Include relevant environment and version details when reporting compatibility issues.

Contribution guidelines and the code of conduct will be added as the project matures.

## License

The project license has not yet been selected. A license file will be added before the project is distributed as an open-source release.

## Project identity

**TerraLab — Your data platform. On your machine.**

Build. Learn. Experiment. Locally.
