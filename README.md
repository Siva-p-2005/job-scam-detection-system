# Job Scam Detection System (AWS Serverless Project)

## 📖 Project Overview
The Job Scam Detection System is a cloud-based serverless web application designed to identify fraudulent job postings. It analyzes job descriptions submitted by users, detects scam-related keywords, assigns a risk level (LOW, MEDIUM, or HIGH), and stores scan history for future reference.

This project is built using AWS serverless services to ensure scalability, cost efficiency, and minimal maintenance.

---

## 🎯 Objectives
- Detect fake job postings automatically
- Classify job descriptions into LOW, MEDIUM, and HIGH risk
- Store scan history securely
- Demonstrate a real-world AWS serverless architecture

---

## 🏗️ Architecture
The application follows a fully serverless architecture:

User → Amazon S3 (Frontend) → API Gateway → AWS Lambda → DynamoDB

📷 Architecture Diagram:
![Architecture Diagram](architecture/architecture.png)

---

## 🧰 Technologies Used
- Amazon S3 – Static website hosting
- AWS Lambda – Backend logic
- Amazon API Gateway – HTTP API
- Amazon DynamoDB – NoSQL database
- Python – Backend language
- HTML, CSS, JavaScript – Frontend

---

## ⚙️ Features
- Job description scam analysis
- Keyword-based risk detection
- Risk levels: LOW / MEDIUM / HIGH
- Scan history retrieval
- Serverless & cost-effective solution

---

## 🔗 API Endpoints
| Method | Endpoint | Description |
|------|--------|------------|
| POST | /scan | Scan job description |
| GET | /history | Retrieve scan history |

---

## 🚀 How It Works
1. User enters a job description on the web page
2. Frontend sends request to API Gateway
3. API Gateway triggers AWS Lambda
4. Lambda analyzes text and stores result in DynamoDB
5. Risk level is returned and displayed to the user
6. Scan history can be viewed anytime

---

## 🧪 Testing
- Fake job postings are detected as HIGH risk
- Genuine job postings are detected as LOW risk
- All scan records are stored and retrievable

---

## 👨‍💻 Author
siva  
AWS Serverless Major Project
