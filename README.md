# DLP System

**Enterprise Data Loss Prevention Platform — Architecture & Design Repository**

[![Status](https://img.shields.io/badge/status-architecture%20%26%20documentation-blue)](#project-status)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Docs](https://img.shields.io/badge/docs-in%20progress-yellow)](docs/)
[![Stage](https://img.shields.io/badge/stage-pre--implementation-lightgrey)](#project-status)

> **This is a documentation and system design repository.** It describes the architecture, product vision, and engineering plan for an enterprise Data Loss Prevention (DLP) platform. It does **not** contain application, API, frontend, or backend source code.

---

## Table of Contents

- [Overview](#overview)
- [Project Status](#project-status)
- [Problem Statement](#problem-statement)
- [Objectives](#objectives)
- [Core Features](#core-features)
- [High-Level Architecture](#high-level-architecture)
- [Technology Stack](#technology-stack)
- [Security Architecture](#security-architecture)
- [Scalability](#scalability)
- [Repository Structure](#repository-structure)
- [Documentation](#documentation)
- [Screenshots](#screenshots)
- [Future Roadmap](#future-roadmap)
- [License](#license)
- [Author](#author)

---

## Overview

**DLP System** is an enterprise-grade **Data Loss Prevention (DLP)** platform designed to help organizations monitor, detect, and protect sensitive information across managed Windows endpoints.

The platform is designed to provide centralized visibility into endpoint activity, enforce configurable security policies, detect sensitive information at rest and in motion, control removable and peripheral devices, and give security teams the tools to investigate policy violations through a unified web dashboard.

This repository documents the **product architecture, design decisions, feature planning, and deployment strategy** for the platform. It serves as a system design reference — for reviewers, collaborators, and hiring managers — rather than a working codebase.

---

## Project Status

| Item               | Status                                              |
| ------------------ | ---------------------------------------------------- |
| Project Stage      | Architecture & Documentation                          |
| Current Deployment | Locally deployed development environment              |
| Current Purpose    | Testing, validation, and architecture planning         |
| Next Milestone     | Deployment on a public VPS with a static public IP     |
| Planned Security   | HTTPS, reverse proxy, secure endpoint communication    |
| Long-Term Goal     | Production-ready, centralized DLP platform             |

The system is currently deployed in a **local development environment only**, used exclusively for internal testing, functional validation, and architectural iteration. No production or public-facing instance currently exists.

The near-term plan is to deploy the management server to a **public VPS with a static public IP address**, fronted by a reverse proxy and HTTPS termination, so that endpoint agents can register and communicate securely with the platform outside of a local network. Until that milestone is reached, all designs described in this repository should be read as **planned architecture**, clearly separated from what has been implemented and tested locally.

---

## Problem Statement

Organizations of every size face a growing risk of sensitive data leaving their control — whether through malicious insider activity, careless employee behavior, or a complete lack of visibility into what happens on endpoint devices.

**Key risk areas this platform is designed to address:**

- **Insider threats** — employees or contractors intentionally exfiltrating confidential data.
- **Accidental data leakage** — sensitive files shared, copied, or moved without malicious intent.
- **Unauthorized USB usage** — uncontrolled use of removable storage to copy corporate data.
- **Sensitive document exposure** — confidential files (financial records, PII, contracts) accessed or moved without oversight.
- **Clipboard abuse** — copying sensitive data (card numbers, credentials, PII) into unauthorized destinations.
- **File exfiltration** — sensitive files moved, renamed, or copied to untrusted locations or media.
- **Lack of endpoint visibility** — security teams unable to answer "what happened on this machine?" during an investigation.
- **Regulatory compliance requirements** — mandates such as GDPR, HIPAA, PCI DSS, and ISO 27001 requiring demonstrable controls over sensitive data handling.

A centralized DLP platform helps security teams **detect these risks as they occur, enforce policy before data leaves the organization, and reconstruct a clear timeline of events** when investigating an incident — reducing both the likelihood and impact of a data loss event.

---

## Objectives

The platform is designed to:

- Protect confidential and sensitive information across the organization
- Monitor endpoint activity in near real time
- Detect sensitive data movement across files, clipboard, and removable media
- Prevent unauthorized data transfers through policy enforcement
- Enforce granular, configurable security policies
- Improve organization-wide visibility into endpoint behavior
- Simplify and accelerate incident investigations
- Generate compliance-ready reports
- Reduce overall insider risk exposure

---

## Core Features

> The features below describe the **intended design and functional scope** of the platform. Items marked *(Planned)* represent designed-but-not-yet-implemented capabilities.

### Endpoint Agent

A lightweight Windows agent responsible for:

- Endpoint registration with the management server
- Secure agent authentication
- Policy synchronization from the central server
- Periodic heartbeat communication and live status reporting
- Device and hardware inventory collection
- Local event collection (file, clipboard, device, screenshot)
- Offline event caching with sync-on-reconnect
- Secure, encrypted communication with the management server
- Self-update support *(Planned)*

See [docs/endpoint-agent.md](docs/endpoint-agent.md) for full design details.

### File Activity Monitoring

Monitors and records key file system operations:

- File creation
- File deletion
- File modification
- File rename
- File move
- File copy
- Save As operations

Each captured event includes contextual metadata:

| Field | Description |
|---|---|
| Username | User account associated with the activity |
| Device | Endpoint hostname / device identifier |
| Timestamp | Date and time of the event |
| Process Name | Application or process that performed the action |
| File Name | Name of the affected file |
| File Path | Full path of the affected file |
| Extension | File extension/type |
| File Size | Size of the file at time of event |
| SHA-256 Hash | Cryptographic hash for integrity and identification |
| Risk Classification | Policy-derived risk level for the event |

### Sensitive Data Detection

Detection patterns planned for identifying sensitive information within files and content streams:

- PAN numbers
- Aadhaar numbers
- Credit card numbers
- Passport numbers
- Bank account numbers
- IFSC codes
- Email addresses
- Phone numbers
- Confidential keywords
- Custom keywords
- Custom regular expressions

**Future direction:** AI-assisted document classification to identify sensitive content beyond static pattern matching *(Roadmap)*.

### Clipboard Monitoring

Monitors clipboard copy operations to detect sensitive information being copied, and raises policy-based alerts when matches occur — helping prevent sensitive data from being pasted into unauthorized destinations (chat apps, personal email, external websites).

### Screenshot Monitoring

Policy-controlled screenshot capture for investigation and audit purposes, with configurable capture conditions:

- Scheduled capture (interval-based)
- Capture on policy violation
- High-risk application monitoring (e.g., capture when specific applications are in focus)

### Device Control

Centralized control over peripheral and removable devices:

- USB storage
- Bluetooth
- Wi-Fi
- Ethernet
- External storage devices

**Supported policy actions:**

| Action | Description |
|---|---|
| Allow | Device access permitted without restriction |
| Block | Device access denied entirely |
| Read Only | Device accessible in read-only mode |
| Notify | Access allowed, user/admin notified |
| Audit Only | Access allowed, activity logged for review |

### Policy Engine

A centralized policy engine designed to evaluate and enforce:

- Device restrictions
- Sensitive file protection rules
- Keyword detection
- Regular expression matching
- Department-based policies
- User-based policies
- Time-based rules (e.g., restrict access outside business hours)
- Risk-based enforcement (adaptive response based on computed risk score)

See [docs/policy-engine.md](docs/policy-engine.md) for design details.

### Dashboard

A centralized web dashboard providing:

- Endpoint inventory
- Agent health monitoring
- Live status view
- Policy management
- Device management
- Incident dashboard
- Alert center
- Audit logs
- Reports
- User management
- Risk overview

See [docs/dashboard.md](docs/dashboard.md) for design details.

### Incident Management

Incidents follow a defined investigation lifecycle:

```
Open → Investigating → Resolved → Closed
```

Each incident record is designed to contain:

- Severity
- User
- Endpoint
- Triggered policy
- Timestamp
- Evidence
- Status

See [docs/incident-management.md](docs/incident-management.md) for full workflow details.

### Reporting

Planned reporting capabilities:

- Endpoint health
- Sensitive data events
- USB activity
- Clipboard events
- File activity
- Policy violations
- User activity
- Incident trends
- Executive summary
- Compliance reports

**Planned export formats:** PDF, CSV, Excel.

See [docs/reporting.md](docs/reporting.md) for design details.

---

## High-Level Architecture

```text
                  +----------------------+
                  |   React Dashboard    |
                  +----------+-----------+
                             |
                    REST API / WebSocket
                             |
                  +----------+-----------+
                  | Node.js + Express    |
                  | Management Server    |
                  +----------+-----------+
                             |
                        PostgreSQL
                             |
                 Policy & Event Processing
                             |
                    Secure HTTPS Channel
                             |
     ------------------------------------------------------
       Windows Agent    Windows Agent    Windows Agent
```

A Mermaid version of this diagram, along with component-level breakdowns, is available in [docs/architecture.md](docs/architecture.md) and [diagrams/](diagrams/).

---

## Technology Stack

### Frontend

| Technology | Purpose |
|---|---|
| React | Component-based UI for the administrative dashboard |
| TypeScript | Type-safe application development |
| Tailwind CSS | Consistent, utility-first responsive styling |

**Why:** A modern, responsive administrative dashboard that scales across endpoint fleets of varying sizes while remaining maintainable.

### Backend

| Technology | Purpose |
|---|---|
| Node.js | JavaScript runtime for the management server |
| Express.js | REST API framework for policy management, authentication, and event processing |

**Why:** A mature, well-understood ecosystem for building secure REST APIs with strong community and library support for authentication, validation, and middleware security controls.

### Database

| Technology | Purpose |
|---|---|
| PostgreSQL | Storage of users, endpoints, policies, incidents, audit logs, and reports |

**Why:** A relational database provides strong consistency guarantees, mature access control, and rich query capabilities needed for compliance-grade audit trails and reporting.

### Endpoint Agent

| Technology | Purpose |
|---|---|
| C# | Primary language for the Windows agent |
| .NET | Native Windows integration and system-level monitoring |

**Why:** .NET provides first-class access to Windows APIs required for file system monitoring, device control, and clipboard/screen capture, with strong performance characteristics for a background service.

### Infrastructure

| Technology | Purpose |
|---|---|
| Ubuntu Server | Host operating system for the management server |
| Nginx | Reverse proxy and TLS termination |
| HTTPS *(planned)* | Encrypted transport for all client/agent communication |
| Public VPS *(planned)* | Externally reachable, static-IP hosting for production deployment |

**Why:** A well-supported, secure, and cost-effective foundation for centralized management server deployment, with a clear path from local development to public production hosting.

---

## Security Architecture

The platform's security model is designed around the following controls:

- **HTTPS communication** for all client, dashboard, and agent traffic
- **JWT authentication** for dashboard and API sessions
- **Role-Based Access Control (RBAC)** for administrators, analysts, and auditors
- **Secure password hashing** (industry-standard adaptive hashing algorithms)
- **API authentication** for all management-server endpoints
- **Endpoint authentication** to ensure only registered, trusted agents can submit data
- **Audit logging** of administrative and policy-related actions
- **Tamper detection** for agent and event integrity
- **Principle of Least Privilege** applied across roles, services, and endpoint permissions

Full details are documented in [docs/security.md](docs/security.md).

---

## Scalability

The architecture is designed to scale from small business deployments to large enterprise environments, with the following capabilities planned as the platform matures:

- Multi-tenant deployments
- Horizontal scaling of the management server
- Load balancing across server instances
- High availability configurations
- Cloud-native deployment options

---

## Repository Structure

This repository contains **documentation only**.

```text
.
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── CHANGELOG.md
├── docs/
│   ├── architecture.md
│   ├── deployment.md
│   ├── endpoint-agent.md
│   ├── policy-engine.md
│   ├── dashboard.md
│   ├── reporting.md
│   ├── incident-management.md
│   ├── security.md
│   └── roadmap.md
├── architecture/
├── diagrams/
├── images/
└── assets/
```

No `frontend/`, `backend/`, or `agent/` source directories are included, as this repository is scoped to system design and product documentation.

---

## Documentation

| Document | Description |
|---|---|
| [Architecture](docs/architecture.md) | Detailed system architecture and component design |
| [Deployment](docs/deployment.md) | Current and planned deployment strategy |
| [Endpoint Agent](docs/endpoint-agent.md) | Windows agent design and responsibilities |
| [Policy Engine](docs/policy-engine.md) | Policy model and enforcement design |
| [Dashboard](docs/dashboard.md) | Dashboard modules and UX design |
| [Reporting](docs/reporting.md) | Reporting and export design |
| [Incident Management](docs/incident-management.md) | Incident lifecycle and workflow |
| [Security](docs/security.md) | Security architecture and controls |
| [Roadmap](docs/roadmap.md) | Future roadmap and long-term vision |

---

## Screenshots

Screenshots will be added as the dashboard and agent reach a demonstrable state in the local development environment.

| View | Status |
|---|---|
| Dashboard Overview | *Coming soon* |
| Endpoint Inventory | *Coming soon* |
| Policy Management | *Coming soon* |
| Device Control | *Coming soon* |
| Incident Dashboard | *Coming soon* |
| Reports | *Coming soon* |
| Analytics | *Coming soon* |

Screenshot assets will be placed under [images/](images/) and referenced from this section as they become available.

---

## Future Roadmap

The following capabilities are identified as **future roadmap items** and are not part of the current design scope:

- AI-powered document classification
- OCR scanning of images and documents
- Browser upload protection
- Email DLP
- Cloud storage monitoring
- Microsoft 365 integration
- Google Workspace integration
- Linux agent
- macOS agent
- Endpoint risk scoring
- User and Entity Behavior Analytics (UEBA)
- SIEM integration
- SOAR integration
- Compliance dashboards (ISO 27001, PCI DSS, HIPAA, GDPR)

See [docs/roadmap.md](docs/roadmap.md) for the full roadmap and prioritization notes.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

## Author

| | |
|---|---|
| **Name** | Ritvik Singh |
| **GitHub** | [github.com/r1tv1kx](https://github.com/r1tv1kx) |
| **LinkedIn** | [linkedin.com/in/ritviksingh14](https://www.linkedin.com/in/ritviksingh14/) |
| **Portfolio** | [ritviksingh.in](https://ritviksingh.in) |

---

<p align="center"><sub>DLP System — Architecture & Documentation Repository</sub></p>
