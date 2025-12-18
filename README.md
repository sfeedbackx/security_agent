# Web Security AI Agent (To Evaluate)

 Autonomous vulnerability detection through dynamic traffic analysis and static source code
 inspection .

Project Status: Research Phase

---

## Project Overview

An intelligent security testing system that automatically:
- Analyzes web traffic to detect attacks (SQLi, XSS, session anomalies)
- Inspects frontend code for vulnerabilities and backend (?) (DOM XSS, secrets, insecure libraries)
- Correlates findings from both sources for high-confidence detection
- Generates reports with actionable recommendations

**Key Principle:** Agents do the thinking. LLM just formats the report.

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                  WEB APPLICATION                        │
└────────────────┬────────────────────────────────────────┘
                 │
        ┌────────┴───────┐
        │                │
┌───────▼──────┐  ┌──────▼────────┐
│   AGENT 1    │  │   AGENT 2     │
│   Traffic    │  │   Code        │
│   Analyzer   │  │   Inspector   │
└───────┬──────┘  └──────┬────────┘
        │                │
        │  ┌─────────────┘
        │  │
   ┌────▼──▼──────┐
   │  Correlation │
   │   Engine     │
   └──────┬───────┘
          │
   ┌──────▼───────┐
   │    Report    │
   │  Generator   │
   └──────────────┘
```

---

## Agent Specifications

### Agent 1: Traffic Analysis

**Input Sources:**
- OWASP ZAP proxy (captures HTTP/HTTPS traffic)
- Browser automation (Playwright/Selenium generates traffic)

**Processing:**
- CNN Model: Detects malicious payloads (SQLi, XSS patterns)
- LSTM Model: Identifies multi-step attack chains
- BERT Model: Analyzes error messages for info disclosure

**Output:**
```json
{
  "findings": [
    {
      "type": "SQL Injection",
      "severity": "CRITICAL",
      "confidence": 0.96,
      "location": "POST /login?username=...",
      "evidence": "MySQL error in response"
    }
  ]
}
```

---

### Agent 2: Code Analysis

**Input Sources:**
- Playwright crawler (extracts HTML + JavaScript)
- External JS files (downloaded from script tags)
- Backend code (optional, if provided)

**Processing:**
- AST Parser: Converts code to Abstract Syntax Tree
- Taint Analysis: Tracks user input to dangerous sinks
- GNN Model: Analyzes code structure for vulnerability patterns
- Pattern Matching: Detects hardcoded secrets, insecure functions

**Output:**
```json
{
  "findings": [
    {
      "type": "DOM-based XSS",
      "severity": "HIGH",
      "confidence": 0.89,
      "location": "js/search.js:42",
      "code": "innerHTML = userInput",
      "taint_path": "location.search → innerHTML"
    }
  ]
}
```

---

### Correlation Engine

**Purpose:** Combine evidence from both agents

**Logic Examples:**

| Traffic Finding | Code Finding | Result |
|----------------|--------------|---------|
| SQLi detected | No backend access | Suspected (Medium Confidence) |
| XSS reflected | innerHTML sink found | CONFIRMED (High Confidence) |
| Session anomaly | No CSRF tokens | CRITICAL (Combined Risk) |

**Output:** Ranked vulnerabilities with combined confidence scores

---

## Deep Learning Models


### Models to Research

**Traffic Analysis:**
- CNN for payload classification
- LSTM for session sequence analysis
- BERT for error message NLP

**Code Analysis:**
- GNN (Graph Neural Networks) for AST analysis
- GraphCodeBERT for semantic code understanding
- Taint analysis with neural networks

**Research Questions:**
1. Which CNN architecture works best for HTTP payloads?
2. How to represent code as graphs for GNN input?
3. What's the optimal sequence length for LSTM session analysis?
4. How to reduce false positives in practice?

---

## Research Topics

###  Traffic Analysis & DL Models

**Topics:**
- OWASP ZAP API integration
- CNN architectures for text classification
- LSTM for sequence modeling
- Training data: CSIC 2010, CICIDS2017

**Key Papers:**
- "Deep Learning for Network Intrusion Detection"
- "Convolutional Neural Networks for Text Classification"

---

###  Code Analysis & AST

**Topics:**
- JavaScript AST parsing (Acorn, Babel)
- Taint analysis algorithms
- Graph Neural Networks (GNN)
- GraphCodeBERT architecture

**Key Papers:**
- "Devign: Effective Vulnerability Identification by Learning GNNs"
- "GraphCodeBERT: Pre-training Code Representations with Data Flow"

---

###  Correlation & Integration

**Topics:**
- Multi-modal learning (combining traffic + code data)
- Knowledge graphs for security
- Confidence scoring algorithms
- Agent orchestration patterns

**Key Papers:**
- "Multi-Modal Deep Learning"
- "Knowledge Graphs for Cybersecurity"

---

###  Evaluation & Testing

**Topics:**
- Vulnerability datasets (Devign, SARD, Juliet)
- Evaluation metrics (Precision, Recall, F1)
- Test applications (DVWA, WebGoat)
- Baseline comparisons

**Key Papers:**
- "Metrics for Evaluating Vulnerability Detection Systems"
- "Benchmarking Security Testing Tools"

---

## Technology Stack (To Evaluate)

### Network Scanning:
- OWASP ZAP (primary) vs mitmproxy
- Playwright vs Selenium

### Code Analysis:
- Acorn vs Babel vs Esprima (JS parsing)
- Tree-sitter (multi-language AST)

### Deep Learning:
- PyTorch vs TensorFlow
- HuggingFace Transformers
- PyTorch Geometric (for GNN)

### Report Generation:

---

## Case Study Design

### Test Target:

**DVWA (Damn Vulnerable Web Application)**
- Known vulnerabilities (ground truth available)
- Multiple severity levels
- Active community support

### Evaluation Plan:

```
1. Baseline: Run pure OWASP ZAP (no AI)
2. Enhanced: Run our agent with DL models
3. Compare: Detection rate, false positives, time
4. Metrics: Precision, Recall, F1-Score
```

### Success Criteria:

- Detect 100% of critical vulnerabilities
- Less than 10% false positive rate
- Complete scan in under 30 minutes
- Generate actionable report

---


## Open Questions

1. **Input Source:** Should we support backend code analysis or frontend-only?
2. **DL Complexity:** Start with simple CNN or jump to transformers?
3. **Dataset:** Use public datasets or generate our own? 
    - **Answer** : public datasets
4. **Scope:** Focus on 2-3 vulnerability types or try to detect everything?
5. **False Positives:** What's acceptable? 5%? 10%?


---

### Repository Structure:

```
/docs          - All documentation
/research      - Papers and notes
/data          - Datasets and training data
/models        - DL model implementations
/agents        - Agent code
/tests         - Test cases
/reports       - Generated outputs
```

