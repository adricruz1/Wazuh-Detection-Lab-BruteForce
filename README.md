# 🛡️ Wazuh Detection Lab - Brute Force Detection (MITRE T1110)

Laboratório de Segurança da Informação desenvolvido para demonstrar a implementação de um ambiente SIEM utilizando **Wazuh**, simulando ataques de força bruta contra um endpoint Windows e criando regras personalizadas para aumentar a criticidade dos alertas.

---

# 🎯 Objetivo

Construir um laboratório de Blue Team capaz de:

- Detectar ataques Brute Force
- Monitorar eventos do Windows
- Correlacionar eventos utilizando MITRE ATT&CK
- Criar regras personalizadas
- Validar alertas utilizando Wazuh Logtest
- Demonstrar o fluxo completo de detecção em um ambiente controlado

---

# 🖥️ Ambiente do Laboratório

| Máquina | Função |
|----------|--------|
| Ubuntu Server 22.04 | Wazuh Manager + Indexer + Dashboard |
| Windows 10 | Endpoint Monitorado (Agent) |
| Kali Linux | Máquina atacante utilizando Hydra |

---

# 🏗️ Arquitetura

![Arquitetura] (configs/evidence/architecture.png)

---

# 🔥 Cenário Simulado

Foi realizado um ataque de força bruta utilizando a ferramenta **Hydra**, executada a partir da máquina Kali Linux contra o serviço SMB do Windows 10.

O objetivo foi validar a capacidade do Wazuh em:

- detectar tentativas de autenticação inválidas;
- correlacionar eventos;
- classificar o ataque segundo MITRE ATT&CK;
- gerar alertas em tempo real.

---

# 📊 Dashboard do Wazuh

![Dashboard](configs/evidence/dashboard-wazuh.png)

O Dashboard permitiu visualizar:

- quantidade de eventos
- alertas críticos
- autenticações com falha
- autenticações bem sucedidas
- eventos por agente
- técnicas MITRE ATT&CK

---

# 🚨 Evidências do Ataque

Durante o ataque foram identificados eventos:

- Event ID 4625
- Rule 60204
- Technique T1110 (Brute Force)
- Technique T1078 (Valid Accounts)

![Ataque](configs/evidence/ataque-brute-force-02.jpg)

---

# 📄 Evidência em JSON

Abaixo está um exemplo do evento capturado pelo Wazuh.

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
        "targetUserName": "administrator",
        "workstationName": "kali",
        "status": "0xc000006d"
      },
      "system": {
        "eventID": "4625",
        "severityValue": "AUDIT_FAILURE"
      }
    }
  },
  "rule": {
    "level": 10,
    "description": "Multiple Windows logon failures.",
    "id": "60204",
    "mitre": {
      "id": ["T1110"],
      "technique": ["Brute Force"]
    }
  }
}
```

---

# ⚙️ Regra Personalizada

Uma regra personalizada foi criada no arquivo:

```
/var/ossec/etc/rules/local_rules.xml
```

Objetivos:

- elevar o alerta para Level 12
- destacar ataques críticos
- facilitar automações futuras
- aumentar a prioridade para analistas SOC

Exemplo:

```xml
<rule id="100001" level="12">
    <if_matched_sid>18130</if_matched_sid>
    <description>ALERTA CRÍTICO - Ataque Brute Force Detectado</description>

    <mitre>
        <id>T1110</id>
    </mitre>
</rule>
```

---

# ✅ Validação

Após criar a regra personalizada foram realizados testes utilizando:

```
wazuh-logtest
```

Resultado:

- regra carregada corretamente
- alerta elevado para Level 12
- evento identificado automaticamente

---

# 🛠️ Tecnologias Utilizadas

- Wazuh
- Ubuntu Server
- Windows 10
- Kali Linux
- Hydra
- XML Rules
- MITRE ATT&CK
- Syslog
- Elastic Stack
- Linux

---

# 📈 Resultados Obtidos

✔ Instalação completa do Wazuh

✔ Configuração do Agent Windows

✔ Simulação de ataque real

✔ Detecção automática

✔ Correlação MITRE ATT&CK

✔ Criação de regra personalizada

✔ Validação utilizando Logtest

✔ Dashboard funcional

---

# 🚀 Próximos Passos

- [ ] Active Response para bloqueio automático do IP atacante
- [ ] Integração com Sysmon
- [ ] Integração com VirusTotal
- [ ] Alertas via Telegram
- [ ] Dashboard customizado
- [ ] Automatização da resposta ao incidente

---

# ⚠️ Aviso

Este laboratório foi desenvolvido exclusivamente para fins educacionais, estudos em Segurança da Informação e composição de portfólio.

Nenhum ambiente de produção foi utilizado e nenhuma informação sensível foi exposta.
