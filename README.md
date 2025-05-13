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