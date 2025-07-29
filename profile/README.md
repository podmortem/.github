# Podmortem

> Intelligent postmortem pod failure analysis

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

**Podmortem** is a Kubernetes-native operator that automatically analyzes pod failures and provides intelligent explanations of what went wrong. Instead of manually digging through thousands of log lines, Podmortem uses community-driven pattern libraries and AI integration to identify root causes and suggest remediation steps.

## What Problem Does It Solve?

When pods fail in Kubernetes or OpenShift, operations teams typically spend significant time manually analyzing logs to understand what went wrong. Podmortem automates this initial analysis, providing immediate insights and reducing the burden of manual investigation. Podmortem delivers:

- **Instant root cause identification** from cryptic error logs
- **Community-driven pattern libraries** for common failure scenarios
- **AI-powered explanations** in human-readable language
- **Actionable remediation suggestions**

## How It Works

Podmortem combines **deterministic pattern matching** with **AI-powered explanation** through a two-layer approach:

```
Pod Failure → Log Collection → Pattern Analysis → AI Explanation → Human-Readable Report
```

### Layer 1: Pattern-Driven Analysis
- **Precise Detection**: Regex-based patterns identify known failure signatures
- **Community Knowledge**: Extensible pattern libraries for frameworks like Quarkus, Spring Boot, etc.
- **Smart Scoring**: Multi-dimensional scoring considers severity, proximity, and temporal relationships

### Layer 2: AI-Powered Explanation  
- **Natural Language**: Transform technical analysis into clear explanations
- **Context Aware**: AI understands broader failure context
- **Provider Agnostic**: Works with OpenAI, Ollama, vLLM, or enterprise AI services

## Architecture

| Component | Purpose | Technology |
|-----------|---------|------------|
| **Operator** | Kubernetes controller managing podmortem lifecycles | Quarkus + Kubernetes Java SDK |
| **Log Parser** | Pattern matching and context extraction engine | Quarkus REST services |
| **AI Interface** | Connects to various AI providers for explanations | Quarkus with provider integrations |
| **AI Provider Library** | Implements provider SDKs (OpenAI-compatible, Ollama, vLLM) and prompt engine | Java library |
| **Common Library** | Shared models, utilities, and CRD definitions | Java library |

## Pattern Libraries

Podmortem uses YAML-based pattern libraries to identify failure signatures:

```yaml
patterns:
  - id: "quarkus_startup_failure"
    name: "Quarkus Application Startup Failure"
    
    primary_pattern:
      regex: "Failed to start|startup failed"
      confidence: 0.95
    
    secondary_patterns:
      - regex: "RuntimeException"
        weight: 0.4
        proximity_window: 20
    
    severity: "CRITICAL"
    category: ["startup", "runtime"]
    
    remediation:
      description: "Quarkus application failed to start"
      common_causes:
        - "Configuration errors"
        - "Missing dependencies"
        - "Port binding conflicts"
```

## Quick Start

### Prerequisites
- Kubernetes cluster
- kubectl configured
- AI provider (OpenAI API key or local Ollama)

### Installation

1. **Clone the charts repo** (temporary until a remote Helm repo is published):
```bash
git clone https://github.com/podmortem/helm-charts.git
cd helm-charts
```

1. **Install the chart**:
```bash
helm upgrade --install podmortem ./charts/podmortem-operator \
  --create-namespace -n podmortem-system
```

1. **Configure an AI provider** (example for OpenAI):
```bash
# Replace YOUR_KEY
kubectl create secret generic openai-secret -n podmortem-system \
  --from-literal=api-key=YOUR_KEY

# Create AIProvider resource referencing the secret (simplified)
kubectl apply -n podmortem-system -f - <<EOF
apiVersion: podmortem.redhat.com/v1alpha1
kind: AIProvider
metadata:
  name: openai-provider
spec:
  providerId: openai
  apiUrl: https://api.openai.com/v1
  modelId: gpt-4o
  authenticationRef:
    secretName: openai-secret
    secretKey: api-key
EOF
```

1. **Create a Podmortem monitor CR**:
```bash
kubectl apply -n podmortem-system -f - <<EOF
apiVersion: podmortem.redhat.com/v1alpha1
kind: Podmortem
metadata:
  name: default-monitor
spec:
  podSelector:
    matchLabels:
      app: backed-api
  aiProviderRef:
    name: openai-provider
  patternLibraryRef:
    name: quarkus-patterns
EOF
```

1. **Check status:**
```bash
kubectl get podmortems -n podmortem-system -o yaml
```
