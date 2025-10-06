# RJG_Career_Bot
http://rajiv-portfolio-chatbot-website.s3-website-us-east-1.amazonaws.com

Chatbot leveraging LLMs deployed using AWS - Specifically to answer HR questions about my experience.
# Project Summary: AI-Powered Portfolio Chatbot

## Project Overview

**What Was Built:**
An interactive, AI-powered chatbot that answers questions about your professional background in real-time. Visitors can ask natural language questions and receive intelligent responses based on my actual resume, recommendations, personality profiles, and project documentation.

**Intended Audience:**
- **Primary:** Recruiters and hiring managers who want to learn about your background quickly
- **Secondary:** Networking contacts, potential clients, or anyone interested in your professional experience
- **Use Case:** Acts as a 24/7 virtual assistant that can answer questions like "What work experience do you have?", "Tell me about your skills", or "What projects have you worked on?"

**Live Chatbot URL:**
```
http://rajiv-portfolio-chatbot-website.s3-website-us-east-1.amazonaws.com
```

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────┐
│                    PUBLIC INTERNET                       │
│                  (Recruiters/Visitors)                   │
└────────────────────────┬────────────────────────────────┘
                         │
                         ↓
┌─────────────────────────────────────────────────────────┐
│  AWS S3 Static Website Hosting                          │
│  • Hosts HTML/CSS/JavaScript chatbot interface          │
│  • Public URL for visitors                              │
│                                   │
└────────────────────────┬────────────────────────────────┘
                         │ (HTTPS API Call)
                         ↓
┌─────────────────────────────────────────────────────────┐
│  AWS API Gateway                                         │
│  • Public REST API endpoint                             │
│  • Rate limiting: 10 req/sec, 100/day                   │
│  • CORS enabled for browser access                      │
│                   │
└────────────────────────┬────────────────────────────────┘
                         │ (Triggers)
                         ↓
┌─────────────────────────────────────────────────────────┐
│  AWS Lambda                                              │
│  • Serverless function (Python 3.12)                    │
│  • Processes user queries                               │
│  • Validates input (length, rate limiting)              │
│                   │
└────────────────────────┬────────────────────────────────┘
                         │ (Queries)
                         ↓
┌─────────────────────────────────────────────────────────┐
│  Amazon Bedrock - Knowledge Base                        │
│  • AI-powered semantic search                           │
│  • Understands natural language questions               │
│  • Retrieves relevant document sections                 │
│  • Generates human-like responses                       │
│                     │
└────────────────────────┬────────────────────────────────┘
                         │ (Retrieves from)
                         ↓
┌─────────────────────────────────────────────────────────┐
│  Amazon OpenSearch Serverless                            │
│  • Vector database for document embeddings              │
│  • Enables fast semantic search                         │
│                    │
└────────────────────────┬────────────────────────────────┘
                         │ (Indexed from)
                         ↓
┌─────────────────────────────────────────────────────────┐
│  AWS S3 Storage (Documents Bucket - Private)            │
│  • Stores your resumes, recommendations, profiles       │
│  • Private and secure                                   │
│                           │
└─────────────────────────────────────────────────────────┘
```

---

## AWS Services Used - Detailed Breakdown

### **1. AWS IAM (Identity and Access Management)**
**Purpose:** Security and permissions management
**What Was Created:**
- IAM user: `portfolio-admin` (for you to manage AWS)
- IAM roles: `LambdaBedrockExecutionRole`, `AmazonBedrockExecutionRole`
- Policies: Granted specific permissions to each service



---

### **2. Amazon S3 (Simple Storage Service)**
**Purpose:** Two separate buckets with different purposes

**Bucket 1: Documents Storage (Private)**
- Name: `[your-name]-portfolio-chatbot`
- Contents: Resumes, LinkedIn recommendations, personality profiles, project docs
- Security: Private, blocked from public access
- Structure: Organized folders (resumes/, recommendations/, projects/, etc.)

**Bucket 2: Website Hosting (Public)**
- Name: `rajiv-portfolio-chatbot-website`
- Contents: HTML/CSS/JavaScript chatbot interface
- Security: Public read access for website visitors
- Features: Static website hosting enabled


---

### **3. Amazon Bedrock**
**Purpose:** AI foundation models and Knowledge Base

**What We Used:**
- **Foundation Model:** Claude 3.5 Haiku or Amazon Titan Text (for generating responses)
- **Embeddings Model:** Titan Embeddings G1 - Text (for document indexing)
- **Knowledge Base:** PortfolioKnowledgeBase
  - Automatically reads your S3 documents
  - Chunks them into searchable pieces
  - Creates semantic embeddings
  - Retrieves relevant sections for each query
  - Generates natural language answers

**Features:**
- Understands context and nuance
- Can synthesize information from multiple documents
- Cites sources (shows which documents were used)
- Handles complex, open-ended questions



---

### **4. Amazon OpenSearch Serverless**
**Purpose:** Vector database for semantic search

**What It Does:**
- Stores mathematical representations (embeddings) of your documents
- Enables fast similarity search
- Required by Bedrock Knowledge Base
- Fully managed (no servers to configure)

**Configuration:**
- Collection: `portfolio-kb-collection`
- Index: `portfolio-index`
- Automatically scaled by AWS



---

### **5. AWS Lambda**
**Purpose:** Serverless code execution

**Function Details:**
- Name: `PortfolioChatbotFunction`
- Runtime: Python 3.12
- Handler: `lambda_function.lambda_handler`
- Timeout: 60 seconds
- Memory: 128 MB

**What It Does:**
- Receives query from API Gateway
- Validates input (length check, prevents abuse)
- Calls Bedrock Knowledge Base
- Returns formatted response
- Handles errors gracefully

**Libraries Used:**
- `boto3` (AWS SDK for Python)
- `json` (response formatting)


---

### **6. Amazon API Gateway**
**Purpose:** Creates public API endpoint for your chatbot

**API Details:**
- Name: `PortfolioChatbotAPI`
- Type: REST API
- Endpoint: `https://zmg42unnt3.execute-api.us-east-1.amazonaws.com/prod/chat`
- Method: POST
- Stage: `prod`

**Features Configured:**
- **CORS:** Enabled (allows browser requests)
- **Rate Limiting:** 10 requests/second, burst up to 20
- **Usage Plan:** 100 requests per day quota
- **Lambda Proxy Integration:** Direct connection to Lambda

**Security:**
- Input validation at Lambda level
- Rate limiting prevents abuse
- CORS configured for specific origins (currently `*` for testing)



---

### **7. Amazon CloudWatch**
**Purpose:** Logging and monitoring

**What We Configured:**
- Lambda function logs (for debugging)
- API Gateway access logs
- Cost alerts and budgets
- Usage metrics

**Features:**
- Track API request counts
- Monitor Lambda execution times
- Debug errors via logs
- Set up alarms for unusual activity

------

## Technical Achievements

1. ✅ **Deployed cloud infrastructure** across 7+ AWS services
2. ✅ **Configured AI/ML services** (Bedrock, embeddings, vector databases)
3. ✅ **Wrote and deployed serverless functions** (Lambda in Python)
4. ✅ **Built a REST API** with rate limiting and security
5. ✅ **Hosted a public website** with custom domain capability
6. ✅ **Implemented cost controls** and monitoring
7. ✅ **Configured IAM security** with proper role-based access
8. ✅ **Integrated multiple services** into a cohesive application

---

## Real-World Business Value

### **For Job Seekers (Me):**
- **24/7 availability:** Recruiters can learn about you anytime
- **Instant responses:** No waiting for email replies
- **Comprehensive answers:** AI synthesizes information from all your documents
- **Impressive differentiator:** Shows technical initiative and innovation
- **Scalable:** Can handle hundreds of concurrent visitors
- **Trackable:** Can add analytics to see who's interested

### **For Recruiters (My Audience):**
- **Quick screening:** Get answers in seconds vs. reading entire resume
- **Interactive experience:** More engaging than static PDF
- **Detailed information:** Can ask follow-up questions
- **Always current:** You update documents, AI uses latest info
- **Professional impression:** Shows you're forward-thinking

---

## Skills Demonstrated to Employers

**By building this project, I have experience with:**

1. **Cloud Computing:** AWS infrastructure and services
2. **AI/ML:** Bedrock, embeddings, vector databases, LLMs
3. **Serverless Architecture:** Lambda, API Gateway
4. **DevOps:** Deployment, monitoring, cost optimization
5. **Security:** IAM, CORS, input validation, rate limiting
6. **Frontend Development:** HTML, CSS, JavaScript
7. **API Development:** REST APIs, JSON, HTTP
8. **Problem Solving:** Debugging, troubleshooting, iteration
9. **Documentation:** Following technical guides, creating documentation
10. **Project Management:** Completing a complex multi-phase project
