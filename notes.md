AI SECURITY LAB ARCHITECTURE

goal: X not to build a SIEM
      AI agent that can automate repetitive security tasks.

example: security alert -> AI understands it -> AI prioritizes it -> AI suggests remediation -> Human reviews -> Action is taken


**Step-1** Endpoints generate security events
Endpoint: any machine we want to monitor.

Example: Ubuntu Server, Windows Laptop, K8s Node, EC2 Instance

1000s of events happens everyday.
	- User login
	- Failed login
	- File modified
	- Docker container started
	- SQL Injection Detected

These events are called security logs.

**Step-2** Wazuh Agent collects these logs.
Wazuh agent runs on every endpoint.
Its job is to monitor the machine

It watches things like:
	- Who logged in?
	- Which processes started?
	- Which file changed?
	- Any suspicious behavior.

**Step-3 Elastic Search Stores the event**u
Millions of security events are generated everyday.
A relational database like PostgreSQL isn't ideal for searching huge volumes of logs.
Instead, security platforms use Elasticsearch.

Instead of tables, Elastic Search stores JSON documents.

Elasticsearch is preffered because security systems generate millions of semi-structured JSON logs. It provides fast indexing and searching, making it ideal for log analysis and threat detection.

**Step-4 Kibana**
ElasticSearch stores data. 
Kibana visualises it.

Kibana shows:
	- Dashboards
	- Graphs
	- Alerts
	- Search
	- Trends

**Step-5 Wazuh API**
Instead of opening Kibana manually, our AI agent can simply call the REST API

AI Agent -> GET /alerts -> JSON Response

**Step-6 n8n Workflow**
Acts like the brain that connects everything.

New Alert
   |
   V
Call Wazuh API
  |
  V
Recieve JSON
   |
   V
Send to Claude
   |
   V
Recieve Analysis
   |
   V
Create GitHub Issue
   |
   V
Send Slack Notification

n8n is itself not AI
it only orchestrates the flow.
It's like an AUTOMATION PIPELINE

**Step-7 Claude (LLM)**
Claude recieves the alert.

"We give it a skill/prompt"

Claude give us the summary and suggested fix.
It's not executing commands, but reasoning

**Step-8 AI Agent takes action**
AI Agent can now perform actions.
Like:
	-Create Github Issues
	-Assign Developer
	-Generate JIRA ticket
	-Notify Slack
	-Draft Pull Request
	-Generate Security Report

This removes manual repetitive work.

**Step-9 Human in the loop**
AI should not make the final security decisions.

Instead:
	-AI Analyses, prioritizes, recommends, HUMAN APPROVES, Final Action

Because LLMs can hallucinate, Critical Security decisions require human judgement.

**BIGGER PICTURE**
SIEM: Security Information and Event Management

goal: Not to build a SIEM, but to understand how an AI Agent can automate repetitive security operations.

Traditional workflow:
	- security tool
	- human reads alerts
	- human prioritizes
	- human creates tickets
	- human aligns developer

Our Goal is:
	- Security tool
	- AI Agent
	- Summarize
	- prioritize
	- suggest fix
	- create ticket
	- Human approves


**WHY DOCKER?**

instead of installing wazuh directly on my laptop, i deployed it using docker.
docker provides:
	-isolated environment
	-easy setup
	-reproducible deployment
	-easy cleanup

instead of manually installing
	-wazuh manager
	-wazuh dashboard
	-open search
	-supporting services

**DOCKER COMPOSE STARTS EVERYTHING TOGETHER**

Docker compose launches multiple containers.

	-Wazuh Manager - acts as the central brain, recieve logs, manage agents, apply detection rules, generate alerts
	-Wazuh Indexer - stores all generated alerts, Elastic Search, stores JSON Documents
	-Wazuh Dashboard - provides a web interface, viewing alerts, searching logs, monitoring endpoints.

	
**CONNECTING MY LINUX WITH WAZUH**
i installed the wazuh agent, a monitoring agent.
its job is to collect:
	-system logs
	-security events
	-auditd logs

**AUDITD**
i also configured auditd
it monitors linux security events

ex:
	-user login
	-failed login
	-file modification
	-permission changes

**FLOW OF DATA**

Linux machine -> auditd detects events -> wazuh agent collects events -> wazuh manager recieves -> manager applies detection rules -> creates security alerts -> stores alert in Open Search

at this point, alerts stay inside wazuh

**WHY APIs**
if 10k alerts,
human cannot read it,
AI should not be given the access of the entire database

Wazuh exposes RESTAPIs
Apps communicates using APIs.


**POSTMAN TESTING**
before integrating the API into an automation workflow, 
i wanted to understand 
	-authentication
	-endpoints
	-JSON Structure

instead of writing code, i used Postman, Postman is simply an API testing tool.

**NEXT STEP**

Now instead of manually reading 5000 Alerts.
an AI agent can consume the API

Workflow becomes:

Wazuh API -> JSON Alerts -> LLM -> Summarize -> Prioritize -> Recommended Fix -> GitHub Issue -> Jira Ticket
























