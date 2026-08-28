# Introduction to Web Infrastructure Design

This repository contains a set of web infrastructure design exercises showing how
a web application can evolve from a simple single-server setup to more resilient,
secure, monitored, and separated architectures.

The project focuses on understanding the role of each infrastructure component,
how requests move through a web stack, and what limitations appear as traffic,
availability, security, and operational requirements increase.

This is a design project. No server, service, certificate or cloud resource is
deployed, configured or contacted.

## Learning goals

This project covers:

- DNS and A records
- Web servers such as Nginx
- Application servers and application code
- Databases such as PostgreSQL/MySQL
- TCP/IP communication
- Load balancing and redundancy
- Single points of failure (SPOF)
- Active-active and active-passive concepts
- Database primary/replica concepts
- HTTPS, firewalls, and monitoring
- Separated infrastructure tiers
- Capacity and scaling considerations

## Designs

The designs and their explanations live in `web_infrastructure_design/README.md`,
one section per task.

| Task | Design | Files |
| --- | --- | --- |
| 0 | Model a Single-Server Web Stack | `single_server_stack.mmd`, `single_server_stack.png` |
| 1 | Add Redundancy and Traffic Distribution | `redundant_web_tier.mmd`, `redundant_web_tier.png` |
| 2 | Add Protection and Observability | `protected_monitored_stack.mmd`, `protected_monitored_stack.png` |

## Diagram format

Each design is delivered as a plain-text diagram (`.mmd`) together with a rendered
image (`.png`). The plain-text representation is used instead of Mermaid source
with the agreement of the project supervisor. Every diagram still uses the exact
component labels required by its task, marks the boundaries the task asks for, and
gives every connection a direction.

## Repository structure

```text
holbertonschool-web_infrastructure_design/
├── README.md
└── web_infrastructure_design/
    ├── README.md
    ├── single_server_stack.mmd
    ├── single_server_stack.png
    ├── redundant_web_tier.mmd
    ├── redundant_web_tier.png
    ├── protected_monitored_stack.mmd
    └── protected_monitored_stack.png
```
