# LYNKEUS
## Hybrid Network Intrusion Detection System

> Explainable, open-source, AI-powered network security.

**Project ID:** CS2601
**Institute:** Bharat Institute of Technology, Meerut, Department of Computer Science and Engineering
**Affiliation:** Dr. A. P. J. Abdul Kalam Technical University, Lucknow, Uttar Pradesh
**Academic Year:** 2026-27

---

## Overview

This project is a hybrid network intrusion detection system (NIDS). It captures live network traffic, checks it with a **rule/signature engine** and a **machine-learning engine** in parallel, and compares the two decisions. When both agree, the system raises a **high-confidence alert** (and may auto-block). Otherwise the traffic is logged for review.

Every alert is shown on a dashboard with a plain-language reason, so analysts can see *why* something was flagged.

```
Capture traffic -> Check rules + ML -> Compare decisions -> Show alert / log
```

## Problem Statement

Current tools can leave important gaps in network security:

- **False alerts:** too many harmless activities get flagged.
- **Hard to explain:** it is often unclear why an alert was raised.
- **Encrypted traffic:** HTTPS content cannot be inspected directly.
- **High cost / closed tools:** some enterprise tools are expensive or less transparent.

## Objectives

1. **Reduce false positives:** raise high-confidence alerts only when the rule engine and ML engine agree.
2. **Explain every alert:** provide plain-language reasoning and detection evidence.
3. **Analyse encrypted traffic:** use packet size, timing patterns and simplified TLS fingerprinting, without reading payload content.
4. **Combine detection methods:** signature matching alongside ML and anomaly-detection models.
5. **Support investigation:** store detection logs and show alerts, traffic and system metrics in a dashboard.

## Detection Workflow

1. **Capture packet** (Scapy)
2. **Extract features** (packet/flow features)
3. **Rule engine check** and **ML engine check** (run in parallel)
4. **Both agree?**
   - Yes: high-confidence alert, logged to the dashboard, may trigger auto-block
   - Single-engine match: alert logged for manual review
   - No match: traffic passes and is logged as normal

## System Architecture

```
Network Traffic -> Capture Layer -> Detection Engine -> Flask API -> React Dashboard
                   (Scapy)          (Rules + ML)        (REST)
                                         |
                    +--------------------+--------------------+
                    |                    |                    |
               Prometheus           PostgreSQL             Alerts
           (system & model         (detection logs      (Email, Slack,
               metrics)              & history)             Syslog)
```

The design is **modular**: capture, detection, storage and presentation are separate layers, so future additions (federated learning, honeypots) can be added without a rewrite.

## Modules

| # | Module | Description |
|---|--------|-------------|
| 1 | Packet Capture | Live packet sniffing and traffic collection with Scapy |
| 2 | Feature Extraction | Derive packet/flow features for the rule and ML engines |
| 3 | Rule Engine | Match traffic against known signatures and rules |
| 4 | ML Detection | Classification and anomaly-detection models |
| 5 | Decision Correlation | Compare rule and ML outcomes to prioritise high-confidence alerts |
| 6 | Logging & Dashboard | Store events, display evidence, show alert status and system metrics |

## Tech Stack

| Category | Technology |
|----------|-----------|
| Capture | Python, Scapy |
| Detection / ML | Scikit-learn, XGBoost, TensorFlow; Random Forest, Isolation Forest, LSTM/CNN |
| Explainability | SHAP |
| Backend | Flask, REST API |
| Frontend | React, JavaScript |
| Data & Operations | PostgreSQL, Prometheus |
| Alerting | Email, Slack, Syslog |
| Datasets | KDD99, UNSW-NB15, CIC-IDS2017 |

## Project Status

| Component | Status |
|-----------|--------|
| Core capture and detection pipeline | Running |
| Flask REST API | Functional |
| React dashboard | Functional |
| ML models (KDD99, UNSW-NB15) | In progress |
| SHAP-based explanations | Planned |
| Rule + ML combined detection (final) | Planned |
| Encrypted-traffic analysis module | Planned |
| Benchmarking across all datasets | Planned |

> Quantitative accuracy and performance results will be added after benchmarking on KDD99, UNSW-NB15 and CIC-IDS2017.

## Getting Started

> Update the commands below to match your actual setup.

### Prerequisites

- Python 3.10+
- Node.js 18+
- PostgreSQL 14+
- Root/administrator privileges for live packet capture
- (Optional) Prometheus

### Installation

```bash
# Clone the repository
git clone https://github.com/<your-username>/hybrid-nids.git
cd hybrid-nids

# Backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

# Configure environment
cp .env.example .env            # then edit DB credentials, interface name, etc.

# Frontend
cd frontend
npm install
```

### Running

```bash
# 1. Start the Flask API
python -m backend.app

# 2. Start packet capture + detection (requires elevated privileges)
sudo python -m nids.main --interface eth0

# 3. Start the dashboard
cd frontend && npm start
```

## Datasets

| Dataset | Notes |
|---------|-------|
| KDD99 (KDD Cup 1999) | Classic intrusion-detection benchmark |
| UNSW-NB15 | Modern network-traffic dataset |
| CIC-IDS2017 | Canadian Institute for Cybersecurity dataset |

Datasets are **not** committed to this repository. Download them and place them in `data/raw/` (see `data/README.md`).

## Advantages

- Open-source and transparent design
- Rule + ML agreement can reduce false positives
- Plain-language alert explanations
- Modular capture, detection, storage and presentation layers
- Metadata-based analysis for encrypted traffic

## Limitations

- Performance depends on dataset coverage and feature quality
- Single-engine matches may need manual review
- Encrypted-traffic analysis uses metadata, not payload inspection

## Future Scope

- Complete SHAP-based explanations
- Finalise rule + ML combined detection
- Complete the encrypted-traffic analysis module
- Benchmark against all listed datasets
- Explore federated learning and honeypot integration

## Team

| Name | Role | University Roll No. |
|------|------|---------------------|
| Gaurav Saini | Team Leader | - |
| Aditya Yadav | Team Member | 2301280100015 |
| Aryan Sharma | Team Member | 2301280100018 |

**Supervision:** Faculty Guide, Department of Computer Science and Engineering, Bharat Institute of Technology, Meerut.

## References

1. N. Moustafa and J. Slay, "UNSW-NB15: a comprehensive data set for network intrusion detection systems (UNSW-NB15 network data set)," 2015 Military Communications and Information Systems Conference (MilCIS), 2015, pp. 1-6.
2. KDD Cup 1999 Data: intrusion-detection evaluation dataset.
3. Canadian Institute for Cybersecurity, CIC-IDS2017 dataset.
4. Scapy, scikit-learn, XGBoost, TensorFlow and SHAP official documentation.

## Disclaimer

This project is built for academic and research purposes. Only capture and analyse traffic on networks you own or are authorised to monitor. Auto-blocking should be tested in a controlled lab environment first.

## License

Add a license of your choice (e.g. MIT or Apache-2.0) and include a `LICENSE` file.
