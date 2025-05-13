# Adversary Emulation-as-Code Lab

## Description
Automated lab to simulate adversary techniques (e.g., Pass-the-Ticket) using MITRE Caldera and Atomic Red Team. Uses Sigma rules + ELK for real-time SOC alerting.

## Features
- Emulates techniques like T1550.003 (Pass-the-Ticket)
- ELK Stack integration using simulated pipeline
- Sigma-based alert simulation

## How to Run
```bash
python simulation/run_caldera.py
python detection/elk_pipeline.py
```

## Requirements
- MITRE Caldera
- Atomic Red Team (optional for realistic scripting)
- ELK Stack (for full SOC simulation)
- Python 3.10+
Step 1: Setup MITRE Caldera
📦 Installation
bash
Copy
Edit
git clone https://github.com/mitre/caldera.git --recursive
cd caldera
pip install -r requirements.txt
python server.py --insecure
⚠️ Caldera will be accessible at http://localhost:8888 (default user: red, pass: admin).

✅ Step 2: Atomic Red Team – Simulate T1550.003
🔧 Install Prerequisites
bash
Copy
Edit
git clone https://github.com/redcanaryco/atomic-red-team.git
cd atomic-red-team
pip install -r requirements.txt
▶️ Run T1550.003 Atomic Test
Create the test script under:
atomic-tests/T1550.003/run_tests.ps1

powershell
Copy
Edit
Invoke-AtomicTest T1550.003
Make sure Invoke-AtomicRedTeam.ps1 is imported in your PowerShell session.

✅ Step 3: ELK Stack for Monitoring
📦 Setup ELK (Docker-based)
Place this in elk-setup/docker-compose.yml:

yaml
Copy
Edit
version: "3.8"
services:
  elasticsearch:
    image: elasticsearch:8.12.0
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"

  logstash:
    image: logstash:8.12.0
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    ports:
      - "5044:5044"

  kibana:
    image: kibana:8.12.0
    ports:
      - "5601:5601"
Then run:

bash
Copy
Edit
cd elk-setup
docker-compose up -d
🛠 logstash.conf Example
conf
Copy
Edit
input {
  tcp {
    port => 5044
    codec => json_lines
  }
}
output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
  }
}
✅ Step 4: Deploy Sigma Rule
Place this in sigma-rules/t1550_sigma.yml:

yaml
Copy
Edit
title: Pass-the-Ticket Detection
logsource:
  category: process_creation
  product: windows
detection:
  selection:
    Image|endswith: 'kerberos.exe'
    CommandLine|contains: 'ticket'
  condition: selection
level: high
Use Sigma to Elasticsearch Converter to convert to ES format:


sigmac -t es-qs -c config.yml sigma-rules/t1550_sigma.yml
✅ Step 5: Run & Monitor
🛡️ Deploy Agent on Windows
In agents/windows-agent.ps1:

powershell

# Powershell agent for Caldera
Invoke-WebRequest -Uri http://<your-caldera-ip>:8888/file/agents/sandcat.go-windows.exe -OutFile sandcat.exe
.\sandcat.exe -server http://<your-caldera-ip>:8888 -group red
🔁 Full Pipeline Execution Script
In scripts/run_atomic_tests.sh:


pwsh atomic-tests/T1550.003/run_tests.ps1
In scripts/ingest_logs_to_elk.sh:


# Push logs to logstash (modify as per your format)
cat /path/to/log.json | nc localhost 5044
✅ Final Docs
In README.md:


# Adversary Emulation-as-Code Lab

This lab simulates Pass-the-Ticket attacks using MITRE Caldera and Atomic Red Team. Telemetry is analyzed via ELK Stack, and Sigma rules are used to detect T1550.003 behavior.

## Components
- MITRE Caldera for red team automation
- Atomic Red Team for T1550.003 emulation
- Sigma rules for detection
- ELK Stack for alerting and telemetry

## Setup
1. Clone and start Caldera
2. Launch ELK using docker-compose
3. Deploy Sigma rules
4. Run Atomic test
5. Monitor in Kibana

## Requirements
- Docker
- PowerShell Core
- Python 3.8+
