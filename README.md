# 🛡️ SIEM Lab: Detecção de Ataques de Força Bruta com Wazuh

Este projeto demonstra a implementação de um ambiente de monitoramento de segurança (SIEM) para detectar, analisar e elevar a criticidade de tentativas de invasão via **Brute Force (T1110)** em endpoints Windows 10.

---

## 🏗️ Topologia do Laboratório

* **SIEM:** Wazuh Manager (Ubuntu Server 22.04)
* **Endpoint:** Windows 10 Pro (Wazuh Agent)
* **Atacante:** Kali Linux (Hydra)

---

## ⚔️ Simulação do Incidente (Ataque)

Utilizei o **Hydra** no Kali Linux para simular um ataque de força bruta contra o serviço SMB do Windows. O objetivo foi validar se o Wazuh capturaria a telemetria necessária para identificar o comportamento anômalo em tempo real.

![Visão Geral do Dashboard no Momento do Ataque](dashboard-wazuh.png)

### 🕵️ Detecção e Mapeamento MITRE ATT&CK

O ataque gerou múltiplos eventos de falha de login (**Event ID 4625**). O Wazuh correlacionou esses logs automaticamente com as técnicas:
* **T1110 (Brute Force)**
* **T1078 (Valid Accounts)**

![Alerta de Ataque Brute Force no Dashboard](ataque-brute-force-01.png)

---

## 💎 Evidência Técnica: Estrutura do Alerta (JSON Payload)

Para um Analista de SOC, a análise do JSON é fundamental para entender os campos extraídos e criar automações. Abaixo, o log bruto onde identificamos o IP do atacante (`10.0.0.3`) e a workstation de origem (`kali`).

<details>
  <summary>📂 Clique para expandir o JSON completo</summary>

```json
{
  "agent": {
    "ip": "10.0.0.2",
    "name": "DESKTOP-P94DDFF",
    "id": "001"
  },
  "data": {
    "win": {
      "eventdata": {
        "ipAddress": "10.0.0.3",
        "targetUserName": "adminstrator",
        "workstationName": "kali",
        "status": "0xc000006d"
      },
      "system": {
        "eventID": "4625",
        "severityValue": "AUDIT_FAILURE",
        "computer": "DESKTOP-P94DDFF"
      }
    }
  },
  "rule": {
    "level": 10,
    "description": "Multiple Windows logon failures.",
    "id": "60204",
    "mitre": {
      "technique": ["Brute Force"],
      "id": ["T1110"],
      "tactic": ["Credential Access"]
    }
  },
  "@timestamp": "2026-03-28T14:21:37.733Z"
}
