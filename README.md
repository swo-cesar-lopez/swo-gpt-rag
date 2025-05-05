# Installation Manual: Document Search with GenAI - PoC
## For Oxfam America

*Prepared by: César López - Sre *  
*Date: May 5, 2025*

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Azure Subscription Setup](#azure-subscription-setup)
4. [Environment Preparation](#environment-preparation)
5. [Deploying the Solution](#deploying-the-solution)
6. [Document Ingestion Process](#document-ingestion-process)
7. [Configuration and Customization](#configuration-and-customization)
8. [Testing the Solution](#testing-the-solution)
9. [Knowledge Transfer](#knowledge-transfer)
10. [Maintenance and Updates](#maintenance-and-updates)
11. [Troubleshooting](#troubleshooting)
12. [Appendix](#appendix)

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

1. **Verify Azure Subscription Access**
   
   ```bash
   # Login to Azure
   az login
   
   # List available subscriptions
   az account list --output table
   
   # Set the active subscription
   az account set --subscription "Your-Subscription-ID"
   ```

2. **Enable Required Azure Services**

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

3. **Create a Resource Group**
   
   ```bash
   # Create a resource group in a supported region
   az group create --name oxfam-genai-doc-search-poc --location eastus
   ```

---

## Environment Preparation

1. **Open cloud shell on Azure:**

    ```bash
    gh auth login
    ```

2. **Create a folder:**

    ```bash
    mkdir gpt
    cd gpt
    ```

3. **Login in GitHub:**

    ```bash
    gh auth login
    ```

4. **Clone the Repository**
   
   ```bash
   gh repo clone swo-cesar-lopez/swo-gpt-rag
   ```

2. **Set Up the Python Environment**
   
   ```bash
   # Create a virtual environment
   python -m venv venv
   
   # Activate the virtual environment
   # On Windows
   venv\Scripts\activate
   # On macOS/Linux
   source venv/bin/activate
   
   # Install dependencies
   pip install -r requirements.txt
   ```

3. **Initialize Environment Variables**
   
   Create a `.env` file in the root directory with the following variables:
   
   ```
   # Azure OpenAI configuration
   AZURE_OPENAI_SERVICE=your-openai-service-name
   AZURE_OPENAI_API_KEY=your-openai-api-key
   AZURE_OPENAI_API_VERSION=2023-05-15
   AZURE_OPENAI_ENDPOINT=https://your-openai-service.openai.azure.com/
   AZURE_OPENAI_DEPLOYMENT=your-openai-model-deployment
   
   # Azure AI Search configuration
   AZURE_SEARCH_SERVICE=your-search-service-name
   AZURE_SEARCH_KEY=your-search-api-key
   AZURE_SEARCH_INDEX=your-search-index-name
   
   # Azure Storage configuration
   AZURE_STORAGE_ACCOUNT=your-storage-account-name
   AZURE_STORAGE_KEY=your-storage-account-key
   AZURE_STORAGE_CONTAINER=your-storage-container-name
   
   # Application configuration
   APP_DEBUG=False
   ```

---

## Deploying the Solution

1. **Deploy Azure Resources**
   
   ```bash
   # Deploy resources using ARM template
   az deployment group create \
     --resource-group oxfam-genai-doc-search-poc \
     --template-file deploy/azuredeploy.json \
     --parameters @deploy/parameters.json
   ```

2. **Deploy the Backend Application**
   
   ```bash
   # Deploy the Python backend to Azure App Service
   az webapp up \
     --resource-group oxfam-genai-doc-search-poc \
     --name oxfam-doc-search-api \
     --location eastus \
     --runtime "PYTHON:3.9" \
     --sku B1
   ```

3. **Deploy the Frontend Application**
   
   ```bash
   # Navigate to the frontend directory
   cd frontend
   
   # Install dependencies
   npm install
   
   # Build the frontend
   npm run build
   
   # Deploy to Azure Static Web Apps
   az staticwebapp create \
     --resource-group oxfam-genai-doc-search-poc \
     --name oxfam-doc-search-web \
     --source . \
     --location eastus
   ```

---

## Document Ingestion Process

1. **Prepare Your Documents**
   
   Ensure your documents are in PDF or Word format with clear section headers.

2. **Upload Documents to Azure Storage**
   
   ```bash
   # Upload documents to Azure Storage
   az storage blob upload-batch \
     --account-name your-storage-account-name \
     --account-key your-storage-account-key \
     --destination your-storage-container-name \
     --source ./documents
   ```

3. **Index Documents in Azure AI Search**
   
   ```bash
   # Run the indexing script
   python scripts/index_documents.py
   ```

4. **Verify Indexing Completion**
   
   Check the Azure Portal to confirm that the indexing process has completed successfully.

---

## Configuration and Customization

1. **Customize the User Interface**
   
   Update the frontend configuration file with Oxfam America branding:
   
   ```
   # frontend/src/config.js
   
   export default {
     branding: {
       logo: '/assets/oxfam-logo.png',
       primaryColor: '#6AAD2E', // Oxfam green
       secondaryColor: '#ffffff',
       fontFamily: 'Arial, sans-serif'
     },
     features: {
       showCitations: true,
       showDocumentPreview: true,
       conversationContext: true
     }
   }
   ```

2. **Configure Contact Information**
   
   Set up the contact mapping for when the system cannot find an answer:
   
   ```
   # backend/config/contacts.json
   
   {
     "finance": {
       "name": "Finance Team",
       "email": "finance@oxfam.org"
     },
     "hr": {
       "name": "Human Resources",
       "email": "hr@oxfam.org"
     },
     "it": {
       "name": "IT Support",
       "email": "itsupport@oxfam.org"
     },
     "default": {
       "name": "Help Desk",
       "email": "helpdesk@oxfam.org"
     }
   }
   ```

3. **Configure Response Parameters**
   
   Adjust the model parameters to control response quality and style:
   
   ```
   # backend/config/model_config.json
   
   {
     "temperature": 0.2,
     "top_p": 0.95,
     "max_tokens": 1000,
     "system_message": "You are an assistant for Oxfam America. Your primary role is to answer questions about organizational policies by searching through policy documents. If you don't find information in the policies, suggest contacting the relevant department. Always provide citations to the source documents."
   }
   ```

---

## Testing the Solution

1. **Initial Functionality Test**
   
   Access the application at the deployed URL and test basic functionality:
   
   - Simple queries about policies
   - Complex queries requiring information from multiple documents
   - Questions with no answers in the documents

2. **Document Update Testing**
   
   Test the document update process:
   
   ```bash
   # Remove a document from the index
   python scripts/remove_document.py --document-id "policy-123"
   
   # Upload an updated version
   az storage blob upload \
     --account-name your-storage-account-name \
     --account-key your-storage-account-key \
     --container-name your-storage-container-name \
     --file ./documents/updated-policy.pdf \
     --name policy-123.pdf
     
   # Re-index the document
   python scripts/index_document.py --document-id "policy-123"
   ```

3. **Performance Testing**
   
   Test the system response time with various query types:
   
   ```bash
   # Run performance test script
   python scripts/performance_test.py
   ```

---

## Knowledge Transfer

1. **Architecture Overview**
   
   Review the system architecture with the Oxfam team:
   
   - Azure OpenAI Service: Provides the generative AI capabilities
   - Azure AI Search: Indexes and retrieves document content
   - Azure Storage: Stores the original documents
   - Python Backend: Handles query processing and integration
   - React Frontend: Provides the user interface

2. **Document Management**
   
   Train the team on document management:
   
   - How to add new documents
   - How to update existing documents
   - How to completely remove outdated documents
   - How to verify document indexing

3. **System Monitoring**
   
   Show how to monitor the system:
   
   - Review logs in Azure App Service
   - Monitor Azure OpenAI usage
   - Check Azure AI Search metrics

---

## Maintenance and Updates

1. **Regular Tasks**
   
   - Document re-indexing after updates (monthly or as needed)
   - Cost monitoring
   - Performance monitoring

2. **Scaling the Solution**
   
   Instructions for scaling the solution to production:
   
   - Upgrade Azure service tiers
   - Implement automated document processing
   - Add advanced security features
   - Set up monitoring and alerting

---

## Troubleshooting

Common issues and their solutions:

1. **Slow Response Times**
   
   - Check Azure OpenAI service tier
   - Verify AI Search query complexity
   - Review document indexing quality

2. **Incorrect Answers**
   
   - Check if documents are properly indexed
   - Adjust model parameters
   - Verify document content quality

3. **Failed Document Indexing**
   
   - Check file format compatibility
   - Verify storage container permissions
   - Review indexer configuration

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