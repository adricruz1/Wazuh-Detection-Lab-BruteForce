# 🛡️ SIEM Lab: Detecção de Ataques de Força Bruta com Wazuh

Este projeto demonstra a implementação de um ambiente de monitoramento de segurança (SIEM) para detectar e analisar tentativas de invasão via Brute Force (T1110) em endpoints Windows 10.

---

## 🏗️ Topologia do Laboratório
- **SIEM:** Wazuh Manager (Ubuntu Server 22.04)
- **Endpoint:** Windows 10 Pro (Wazuh Agent)
- **Atacante:** Kali Linux (Hydra)

---

## ⚔️ Simulação do Incidente (Ataque)
Utilizei o **Hydra** no Kali Linux para simular um ataque de força bruta contra o serviço SMB do Windows. O objetivo foi validar se o Wazuh capturaria a telemetria necessária para identificar o comportamento anômalo.

![Visão Geral do Dashboard no Momento do Ataque](evidence/Dashboard.PNG)
### 💎 Evidência Técnica: Estrutura do Alerta (JSON Payload)
Abaixo, apresento o log bruto processado pelo Wazuh. Note a extração precisa do IP do atacante (`10.0.0.3`) e a correlação com a técnica **MITRE T1110**.

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
---

### Detecção Inicial (Logs de Falha):
O ataque gerou múltiplos eventos de falha de login (Event ID 4625). O Wazuh correlacionou esses logs e aplicou as seguintes técnicas do framework MITRE ATT&CK:
- **T1110 (Brute Force)**
- **T1078 (Valid Accounts)**

![Alerta de Ataque Brute Force no Dashboard](evidence/ataque%20brute%20force%203.PNG)

![Detalhes do Log com Mapeamento T1078 e T1110](evidence/Brute%20force%20log%20do%20codigo%20T1110.PNG)

---

## 🛠️ Engenharia de Detecção (Customização de Regras)

Um dos pontos altos do projeto foi a criação de uma regra personalizada no `local_rules.xml` para elevar a criticidade do evento para o **Nível 12 (Crítico)**.

### Desafios Superados (Troubleshooting):
Durante a edição das regras, enfrentei erros de sintaxe XML que impediram o reinício do serviço do Wazuh. 

![Regra XML Customizada para o Nível 12](evidence/regra%20XML%20para%20brute%20force%20ir%20para%20nivel%2012.PNG)

### Validação Final:
Utilizando a ferramenta `wazuh-logtest`, confirmei que o motor de análise agora identifica o ataque como **Level 12 (Crítico)**, superando os níveis 5 e 10 que seriam os padrões.

![Teste de Log com Sucesso (Wazuh-Logtest)](evidence/teste%20Phase3.PNG)

"Finalizando documentação do laboratório com evidências"
---

## 📈 Próximos Passos
- [ ] Implementar **Active Response** para banir automaticamente o IP de origem no Firewall do Windows.
- [ ] Integrar logs de Sysmon para maior visibilidade de processos suspeitos.
- [ ] Estudar cenários de detecção no **TryHackMe** e **LetsDefend**.


