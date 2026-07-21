# Roadmap

This document outlines the future direction of the DLP System platform, beyond the currently designed core feature set described in the main [README](../README.md).

> **All items in this document are future roadmap items.** None are implemented, and inclusion here does not imply a committed timeline — this document reflects long-term product direction and prioritization thinking.

---

## Near-Term Milestones

These items represent the immediate next steps beyond the current local development environment (see [Project Status](../README.md#project-status)):

- Deployment to a public VPS with a static public IP
- HTTPS and reverse proxy configuration for public agent/dashboard connectivity
- Initial screenshots and demo captures of the local development environment

---

## Detection & Classification

- **AI-powered document classification** — using machine learning to classify sensitive content beyond static keyword/regex matching
- **OCR scanning** — extracting and scanning text from images and scanned documents for sensitive content

---

## Expanded Monitoring Surfaces

- **Browser upload protection** — monitoring and controlling file uploads through web browsers
- **Email DLP** — monitoring outbound email content and attachments for sensitive data
- **Cloud storage monitoring** — visibility into sensitive data movement to/from cloud storage services
- **Microsoft 365 integration** — native integration with Microsoft 365 services for expanded coverage
- **Google Workspace integration** — native integration with Google Workspace services for expanded coverage

---

## Platform Expansion

- **Linux agent** — extending endpoint monitoring and enforcement to Linux-based endpoints
- **macOS agent** — extending endpoint monitoring and enforcement to macOS-based endpoints

---

## Analytics & Intelligence

- **Endpoint risk scoring** — computing an aggregate risk score per endpoint based on historical activity and violations
- **User and Entity Behavior Analytics (UEBA)** — identifying anomalous behavior patterns that may indicate insider risk beyond static policy violations

---

## Ecosystem Integration

- **SIEM integration** — forwarding events and incidents to Security Information and Event Management platforms
- **SOAR integration** — enabling automated response workflows through Security Orchestration, Automation, and Response platforms

---

## Compliance

- **Compliance dashboards** for common regulatory frameworks:
  - ISO 27001
  - PCI DSS
  - HIPAA
  - GDPR

---

## Related Documentation

- [README](../README.md)
- [Architecture](architecture.md)
- [Deployment](deployment.md)
