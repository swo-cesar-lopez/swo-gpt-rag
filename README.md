# Installation Manual: Document Search with GenAI - PoC
## For Oxfam America

*Prepared by: César López - SRE*  
*Date: May 5, 2025*

## Table of Contents
1. [Introduction](#introduction)
   - [Scope and Limitations](#scope-and-limitations)
2. [Prerequisites](#prerequisites)
3. [Azure Subscription Setup](#azure-subscription-setup)
4. [Environment Preparation](#environment-preparation)
5. [Appendix](#appendix)
   - [Azure Service Tier Recommendations](#a-azure-service-tier-recommendations)
   - [Resource Configuration Details](#b-resource-configuration-details)
   - [Document Indexing Schema](#c-document-indexing-schema)
   - [Security Considerations](#d-security-considerations)

---

## Introduction

This document provides step-by-step instructions for installing and configuring the Document Search with GenAI Proof of Concept (PoC) solution for Oxfam America. This solution enables users to search within documents, primarily PDFs and Word files, and find answers to questions with references to the source documents.

The PoC is built using:
- Azure OpenAI Service
- Azure AI Search (formerly Azure Cognitive Search)
- Azure App Service
- Python backend
- React frontend

### Scope and Limitations

This PoC has the following limitations:
- Supports up to 1000 total pages across all documents
- Designed for text-based documents (primarily PDFs and Word files)
- No integrations with other systems
- Basic authentication through Microsoft Entra ID

---

## Prerequisites

Before beginning the installation, ensure you have the following:

1. **Azure Subscription** with sufficient permissions to create resources
2. **Document Collection** - Prepare the policy documents to be indexed (PDFs and Word files)
3. **Sample Questions** - A set of test questions with expected answers to validate the solution
4. **Brand Assets** - Oxfam America logo and color codes for UI customization
5. **Azure CLI** installed on your local machine
6. **Git** installed on your local machine
7. **Python 3.8+** for local development and testing

---

## Azure Subscription Setup

### 1. Verify Azure Subscription Access
   
```bash
# Login to Azure
az login

# List available subscriptions
az account list --output table 
```

### 2. Enable Required Azure Services

Ensure the following services are enabled in your subscription:

- Azure OpenAI Service
- Azure AI Search
- Azure App Service
- Azure Storage Account
   
```bash
# Register providers if needed
az provider register --namespace Microsoft.CognitiveServices
az provider register --namespace Microsoft.Search
az provider register --namespace Microsoft.Web
az provider register --namespace Microsoft.Storage
```

---

## Environment Preparation

### In the Cloud Shell

1. **Create the project folder**

   Create a folder for our project

   ```sh
   mkdir gpt
   cd gpt
   ```

2. **Sign in to GitHub**
   
   Authenticate our session with GitHub

   ```sh
   gh auth login
   ```

3. **Clone the repository**
   
   Clone the repository containing the solution code

   ```sh
   gh repo clone swo-cesar-lopez/swo-gpt-rag
   ```

4. **Move to the resources folder**

   Move to the resources folder

   ```sh
   cd swo-gpt-rag
   ```

5. **Initialize the project**
   
   Initialize the project with Azure Developer CLI. 

   Option 1: With specified location

   ```sh
   azd init --location eastus2
   ```
   
   or alternatively:
   
   Option 2: Two-step initialization
   ```sh
   azd init
   azd env set AZURE_LOCATION eastus2
   ```

6. **Environment name**

   Please specify the name of the new environment for your project (e.g., gptrag).

   ```sh
   Enter a new environment name: 
   ```

7. **Sign in to Azure 

   Execute both of these authentication commands: 

   With Azure Developer CLI

   ```sh
   azd auth login
   ```
   
   With standard Azure CLI
   ```sh
   az login
   ```

6. **Start building the infrastructure**

   Starts the deployment of all components
   ```sh
   azd up
   ```

> **Note**: The deployment process may take 35-50 minutes while all necessary Azure resources are being provisioned.
---
## Appendix

### A. Azure Service Tier Recommendations

| Service | Tier for PoC | Tier for Production |
|---------|--------------|---------------------|
| Azure OpenAI | Standard | Standard |
| Azure AI Search | Basic | Standard or S1 |
| Azure App Service | B1 | P1V2 |
| Azure Storage | Standard LRS | Standard GRS |

### B. Resource Configuration Details

Detailed configuration for each Azure resource.

### C. Document Indexing Schema

Schema used for document indexing in Azure AI Search.

### D. Security Considerations

Security best practices for production deployment.

---

*End of Document*