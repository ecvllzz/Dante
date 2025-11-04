# CENTRO DE CONTROLE DO SISTEMA DANTE (CC)
## Parte 2: Plano de Infraestrutura Detalhado para Claude Code

**Versão:** 1.0.0  
**Data:** 2025-11-04

---

## 9. PLANO DE INFRAESTRUTURA CC (continuação)

### 9.2 Arquitetura do CC

```
┌───────────────────────────────────────────────────────────────┐
│              CENTRO DE CONTROLE (Claude Code)                  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │  LAYER 1: KNOWLEDGE & CONTEXT                            │ │
│  │  ├─ Sistema Dante Docs (todos os [D])                    │ │
│  │  ├─ Changelogs & História                                │ │
│  │  ├─ Policies & Governance                                │ │
│  │  ├─ Perfil do Operador (Dadu)                           │ │
│  │  └─ Best Practices & Patterns                            │ │
│  └──────────────────────────────────────────────────────────┘ │
│                          ↓                                      │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │  LAYER 2: REASONING & DESIGN                             │ │
│  │  ├─ Graph-of-Thought Modeling                            │ │
│  │  ├─ Trade-off Analysis                                   │ │
│  │  ├─ Variant Generation (2-3 options)                    │ │
│  │  ├─ Risk Assessment                                      │ │
│  │  └─ Decision Matrices                                    │ │
│  └──────────────────────────────────────────────────────────┘ │
│                          ↓                                      │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │  LAYER 3: ENGINEERING & VALIDATION                       │ │
│  │  ├─ Prompt Engineering (Claude/Gemini/ChatGPT)           │ │
│  │  ├─ Workflow Design                                      │ │
│  │  ├─ Agent Orchestration                                  │ │
│  │  ├─ Policy Enforcement                                   │ │
│  │  ├─ Simulation & Testing                                 │ │
│  │  └─ Quality Auditing                                     │ │
│  └──────────────────────────────────────────────────────────┘ │
│                          ↓                                      │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │  LAYER 4: DELIVERY & DOCUMENTATION                       │ │
│  │  ├─ Prompt Packs (versionados)                           │ │
│  │  ├─ Workflow Specs (diagramas)                           │ │
│  │  ├─ Handoff XMLs (válidos)                               │ │
│  │  ├─ Test Suites                                          │ │
│  │  ├─ Rationales & Design Docs                            │ │
│  │  └─ Troubleshooting Guides                              │ │
│  └──────────────────────────────────────────────────────────┘ │
│                          ↓                                      │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │  LAYER 5: MONITORING & EVOLUTION                         │ │
│  │  ├─ Performance Metrics                                  │ │
│  │  ├─ Quality Scores                                       │ │
│  │  ├─ Anomaly Detection                                    │ │
│  │  ├─ Feedback Loops                                       │ │
│  │  └─ Continuous Improvement                               │ │
│  └──────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────┘
                          ↕
                 [Operador: Dadu]
                 (comandos, feedback, aprovações)
```

### 9.3 Skills do CC para Claude Code

Estas são as Skills especializadas que o CC deve ter acesso e dominar:

#### Skill 1: **docx** (Criação de Documentos)
**Path:** `/mnt/skills/public/docx/SKILL.md`  
**Quando Usar:** Criar documentação formal, relatórios, especificações  
**Outputs:** Documentos .docx profissionais

#### Skill 2: **xlsx** (Análise de Dados)
**Path:** `/mnt/skills/public/xlsx/SKILL.md`  
**Quando Usar:** Métricas, dashboards, comparações de performance  
**Outputs:** Planilhas .xlsx com análise

#### Skill 3: **pptx** (Apresentações)
**Path:** `/mnt/skills/public/pptx/SKILL.md`  
**Quando Usar:** Apresentar evoluções, roadmaps, resultados  
**Outputs:** Apresentações .pptx executivas

#### Skill 4: **skill-builder** (Meta-Skill)
**Path:** `/mnt/skills/user/skill-builder/SKILL.md`  
**Quando Usar:** Criar novas skills personalizadas para o Dante  
**Outputs:** SKILL.md files completos

#### Skill 5: **critical-validator** (Validação)
**Path:** `/mnt/skills/user/critical-validator/SKILL.md`  
**Quando Usar:** Validar prompts, planos, requests antes de executar  
**Outputs:** Validation reports com issues identificados

#### Skill 6: **dante-redator** (Domain-Specific)
**Path:** `/mnt/skills/user/dante-redator/SKILL.md`  
**Quando Usar:** Trabalhar especificamente no agente Redator  
**Outputs:** Votos judiciais, ajustes iterativos

#### Skill 7: **extracting-dante-knowledge** (Knowledge Management)
**Path:** `/mnt/skills/user/extracting-dante-knowledge/SKILL.md`  
**Quando Usar:** Extrair conhecimento de votos finalizados para knowledge base  
**Outputs:** Structured knowledge entries

### 9.4 Comandos do CC

O CC responde a comandos estruturados do operador. Aqui está a especificação completa:

#### `/intake` — Coleta e Saneamento de Escopo

**Objetivo:** Capturar objetivo, restrições, sucesso esperado, artefatos alvo

**Request Schema:**
```json
{
  "objetivo": "string",
  "restricoes": ["string"],
  "sucesso_esperado": "string",
  "artefatos_alvo": ["PromptPack|Workflow|Handoff|Relatorio"],
  "modelos_alvo": ["Claude|Gemini|ChatGPT"],
  "contexto_fornecido": "string|null"
}
```

**Response Schema:**
```json
{
  "intake_report": {
    "objetivo": "string",
    "restricoes_normalizadas": ["string"],
    "sucesso_criterios": ["string"],
    "artefatos_confirmados": ["string"],
    "lacunas": ["string"],
    "risco_inicial": ["string"]
  },
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 123
  }
}
```

**Definition of Done (DoD):**
- ✅ Responder no schema definido
- ✅ Listar lacunas e possíveis fontes dentro do escopo
- ✅ Bloquear fontes não autorizadas → emitir ALERTA

**Exemplo de Uso:**
```
/intake
Objetivo: Criar novo agente "Estilista" para refinamento de linguagem
Restrições: Deve operar no Claude, integrar com Redator
Sucesso: Voto com linguagem mais natural e fluente
Artefatos: PromptPack + Workflow
Modelos: Claude
```

#### `/design` — Gerar Variantes de Soluções

**Objetivo:** Gerar 2-3 variantes (prompts/workflows/agentes) com matriz de trade-offs

**Request Schema:**
```json
{
  "insumos": "string",
  "criterios_decisao": ["string"],
  "alvos": "Prompt|Workflow|Agente"
}
```

**Response Schema:**
```json
{
  "variantes": [
    {
      "id": "v1",
      "descricao": "string",
      "vantagens": ["string"],
      "riscos": ["string"],
      "quando_usar": "string"
    },
    {
      "id": "v2",
      "descricao": "string",
      "vantagens": ["string"],
      "riscos": ["string"],
      "quando_usar": "string"
    }
  ],
  "matriz_tradeoffs": [
    {
      "criterio": "Velocidade",
      "votos": {"v1": "A", "v2": "B", "v3": "C"}
    },
    {
      "criterio": "Qualidade",
      "votos": {"v1": "A", "v2": "A", "v3": "B"}
    }
  ],
  "recomendacao": "string",
  "tests": ["string"],
  "metrics": {"schema_compliance": true, "elapsed_ms": 456}
}
```

**Definition of Done:**
- ✅ ≥2 variantes com critérios comparáveis
- ✅ Recomendação explícita e justificativa
- ✅ Matriz de trade-offs legível
- ✅ Política checklist validada

**Exemplo de Uso:**
```
/design
Insumos: Agente Estilista deve operar entre Redator e Revisor
Critérios: Velocidade, Qualidade de linguagem, Simplicidade de integração
Alvo: Agente
```

#### `/lint` — Auditoria de Prompt/Workflow

**Objetivo:** Linter de prompt/workflow com sugestões de melhoria

**Request Schema:**
```json
{
  "artefato": "string",
  "tipo": "Prompt|Workflow|Handoff"
}
```

**Response Schema:**
```json
{
  "achados": {
    "bloqueadores": ["string"],
    "avisos": ["string"],
    "melhorias": ["string"]
  },
  "fixes_sugeridos": ["string"],
  "metrics": {"schema_compliance": true, "elapsed_ms": 234}
}
```

**Definition of Done:**
- ✅ Classificar achados por severidade (CRITICAL, HIGH, MEDIUM, LOW)
- ✅ Sugerir correções específicas para bloqueadores
- ✅ Fornecer exemplos de como corrigir

**Exemplo de Uso:**
```
/lint
Artefato: [conteúdo do prompt D_Estilista_v1.md]
Tipo: Prompt
```

#### `/simulate` — Dry-Run pelos Gates Dante

**Objetivo:** Passagem seca pelos gates de validação do Sistema Dante

**Request Schema:**
```json
{
  "artefato": "string",
  "contexto": "string|null"
}
```

**Response Schema:**
```json
{
  "gate_results": [
    {
      "gate": "Analista",
      "pass": true,
      "falhas": []
    },
    {
      "gate": "Handoff",
      "pass": true,
      "falhas": []
    },
    {
      "gate": "Redator",
      "pass": true,
      "falhas": []
    },
    {
      "gate": "Revisor",
      "pass": false,
      "falhas": ["VedacaoEmenta", "RastreabilidadeInsuficiente"]
    }
  ],
  "policy_checklist_result": {
    "passes": ["P1", "P3", "P4", "P5", "P6", "P7", "P8"],
    "fails": ["P2"]
  },
  "risco": "Baixo|Médio|Alto",
  "metrics": {"schema_compliance": true, "elapsed_ms": 567}
}
```

**Policy Checklist (referência):**
- P1: Fidelidade aos Autos
- P2: Vedação de Ementa
- P3: Modo Júri
- P4: Rastreabilidade de Jurisprudência
- P5: Vedação de Cópia Integral
- P6: Fidelidade à Blueprint
- P7: Dispositivo Canônico
- P8: Blueprint Antes de Handoff

**Definition of Done:**
- ✅ Relatar passes/falhas por gate e checklist
- ✅ Identificar riscos específicos
- ✅ Sugerir correções para falhas

**Exemplo de Uso:**
```
/simulate
Artefato: [Voto gerado pelo Redator]
Contexto: [Handoff XML + Blueprint relevantes]
```

#### `/handoff` — Gerar/Validar Handoff XML

**Objetivo:** Gerar ou validar HANDOFF (kickoff_redator) conforme [D] Handoff v5.2

**Request Schema:**
```json
{
  "dados_processuais": {
    "numero": "string",
    "orgao": "string",
    "natureza": "string"
  },
  "objetivo_redacao": "string",
  "anexos": ["string"],
  "modo_juri": true|false
}
```

**Response Schema (XML):**
```xml
<kickoff_redator version="5.2">
  <processo>
    <numero>...</numero>
    <orgao>...</orgao>
    <natureza>...</natureza>
  </processo>
  <tipo_peca>voto</tipo_peca>
  <banner_modo_juri enabled="true|false">
    <crime_base>...</crime_base>
    <orientacao>...</orientacao>
  </banner_modo_juri>
  <estrutura_esperada>
    <tem_preliminares>true|false</tem_preliminares>
    <tem_dosimetria>true|false</tem_dosimetria>
    <numeracao>hierarquica</numeracao>
    <secoes_merito>
      <secao>2.1. [Título]</secao>
    </secoes_merito>
  </estrutura_esperada>
  <fundamentos>...</fundamentos>
  <escopo>...</escopo>
  <dispositivo_canonico>...</dispositivo_canonico>
  <nao_fazer>
    <item>Não produzir ementa</item>
    <item>Não copiar sentença</item>
    <item>Não alterar dispositivo</item>
  </nao_fazer>
  <anexos>...</anexos>
</kickoff_redator>
```

**Definition of Done:**
- ✅ XML válido e completo (xmllint pass)
- ✅ Banner de Modo Júri quando aplicável
- ✅ Campos obrigatórios presentes
- ✅ Conformidade com Handoff v5.2 spec

**Exemplo de Uso:**
```
/handoff
Dados: processo=0001234-56.2024.8.24.0000, orgao=TJSC-1ªCCr, natureza=apelacao
Objetivo: Voto sobre apelação criminal - tese de insuficiência probatória
Modo Júri: false
```

#### `/pack` — Empacotar Prompt Pack

**Objetivo:** Empacotar Prompt Pack multi-modelo com versionamento e changelog

**Request Schema:**
```json
{
  "artefatos": ["Prompt|Workflow|Handoff"],
  "versao": "string (semver)"
}
```

**Response Schema:**
```json
{
  "pack": {
    "versao": "v5.3.0",
    "itens": [
      {
        "nome": "D_Maestro",
        "modelo": "Claude",
        "path": "prompts/D_Maestro_v5.3.md",
        "hash": "sha256:..."
      },
      {
        "nome": "D_Analista",
        "modelo": "Gemini",
        "path": "prompts/D_Analista_v5.3.md",
        "hash": "sha256:..."
      }
    ]
  },
  "changelog": [
    "v5.3.0 - Adicionado agente Estilista",
    "v5.3.0 - Refinamento de linguagem no Redator",
    "v5.3.0 - Melhorias no Modo Júri"
  ],
  "metrics": {"schema_compliance": true, "elapsed_ms": 123}
}
```

**Definition of Done:**
- ✅ Changelog detalhado
- ✅ Versões semânticas (MAJOR.MINOR.PATCH)
- ✅ Hashes de integridade
- ✅ Metadados completos

**Exemplo de Uso:**
```
/pack
Artefatos: [D_Maestro_v5.3, D_Analista_v5.3, D_Redator_v5.3, D_Revisor_v5.3, D_Handoff_v5.3]
Versão: v5.3.0
```

#### `/policy` — Checagem de Conformidade

**Objetivo:** Checagem de conformidade e emissão de ALERTA quando necessário

**Request Schema:**
```json
{
  "acao": "Validar|Auditar",
  "alvo": "Prompt|Workflow|Handoff",
  "conteudo": "string"
}
```

**Response Schema:**
```json
{
  "policy_checklist_result": {
    "passes": ["P1", "P3", "P4", "P5", "P6", "P7", "P8"],
    "fails": ["P2"]
  },
  "alertas": [
    {
      "xml": "<alerta_governanca version=\"1.0\">
        <violacao codigo=\"VedacaoEmenta\"/>
        <fonte_politica>[D] Revisor V4.1</fonte_politica>
        <trecho_conflitante>...</trecho_conflitante>
        <impacto>Risco de produzir ementa proibida</impacto>
        <alternativa_compativel>Remover seção ementa do prompt</alternativa_compativel>
        <acao_recomendada>Corrigir</acao_recomendada>
        <necessita_confirmacao>true</necessita_confirmacao>
      </alerta_governanca>"
    }
  ],
  "metrics": {"schema_compliance": true, "elapsed_ms": 345}
}
```

**Códigos de Violação:**
- `VedacaoEmenta`: P2 violado
- `FonteNaoAutorizada`: Fonte externa não permitida
- `RastreabilidadeInsuficiente`: P4 violado
- `BlueprintAusente`: P8 violado
- `CopiaSentenca`: P5 violado
- `ModoJuriIgnorado`: P3 violado
- `DispositivoAlterado`: P7 violado
- `FidelidadeViolada`: P1 violado

**Definition of Done:**
- ✅ Emitir ALERTA XML para toda violação bloqueadora
- ✅ Classificar severidade (CRITICAL, HIGH, MEDIUM, LOW)
- ✅ Sugerir alternativa compatível
- ✅ Indicar se necessita confirmação do operador

**Exemplo de Uso:**
```
/policy
Ação: Validar
Alvo: Prompt
Conteúdo: [D_Estilista_v1.md completo]
```

### 9.5 Ciclo Canônico de Trabalho do CC

Este é o workflow padrão que o CC deve seguir para qualquer tarefa de evolução do Sistema Dante:

```
┌─────────────────────────────────────────────────┐
│ FASE 1: INTAKE                                  │
│                                                  │
│ • Operador fornece: objetivo, restrições, DoD   │
│ • CC executa: /intake                            │
│ • Output: Intake Report com gaps identificados  │
│                                                  │
│ ✅ Gate: Objetivo claro? Fontes autorizadas?    │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ FASE 2: MODELAGEM                               │
│                                                  │
│ • Graph-of-Thought: Mapear opções possíveis     │
│ • Trade-off Analysis: Identificar prós/contras  │
│ • Risk Assessment: Avaliar riscos               │
│                                                  │
│ ✅ Gate: Opções claras? Riscos identificados?   │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ FASE 3: DESIGN                                  │
│                                                  │
│ • CC executa: /design                            │
│ • Output: 2-3 variantes com matriz de trade-offs│
│ • Operador escolhe variante preferida           │
│                                                  │
│ ✅ Gate: Variantes bem definidas? Escolha clara?│
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ FASE 4: VALIDAÇÃO                               │
│                                                  │
│ • CC executa: /lint (auditoria)                  │
│ • CC executa: /simulate (dry-run)                │
│ • CC executa: /policy (conformidade)             │
│ • Output: Validation reports + correções         │
│                                                  │
│ ✅ Gate: Sem bloqueadores? Políticas OK?        │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ FASE 5: ENTREGA                                 │
│                                                  │
│ • CC produz artefatos finais:                    │
│   - Prompt Pack (versionado)                     │
│   - Workflow Diagram                             │
│   - Handoff XML (se aplicável)                   │
│   - Test Suite                                   │
│   - Rationale Document                           │
│   - Troubleshooting Guide                        │
│                                                  │
│ • CC executa: /pack (empacotamento)              │
│                                                  │
│ ✅ Gate: Artefatos completos? Docs atualizados? │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ FASE 6: PÓS-MORTEM                              │
│                                                  │
│ • Registrar aprendizados                         │
│ • Identificar oportunidades de melhoria          │
│ • Atualizar backlog de evolução                  │
│ • Documentar decisões tomadas                    │
│                                                  │
│ ✅ Gate: Lições registradas? Backlog atualizado?│
└─────────────────────────────────────────────────┘
```

### 9.6 Padrões de Engenharia de Prompt

O CC deve seguir padrões específicos para cada plataforma LLM:

#### Para Claude (Anthropic)

**Estrutura Recomendada:**
```xml
<prompt_sistema id="nome_agente" model="Claude" version="vX.Y.Z">
  <identidade_missao>
    [Quem é o agente e qual sua missão]
  </identidade_missao>

  <fontes_e_precedencia>
    <fontes_autorizadas>
      [Lista de fontes de verdade]
    </fontes_autorizadas>
    <restricoes>
      [O que não pode fazer]
    </restricoes>
    <precedencia>
      [Ordem de prioridade das fontes]
    </precedencia>
  </fontes_e_precedencia>

  <task>
    [Descrição clara da tarefa]
  </task>

  <instructions>
    [Instruções passo a passo]
    <step id="1">[...]</step>
    <step id="2">[...]</step>
  </instructions>

  <response_format>
    [Formato esperado de output]
    [JSON Schema se aplicável]
  </response_format>

  <examples>
    <example>
      <input>[...]</input>
      <output>[...]</output>
      <rationale>[...]</rationale>
    </example>
  </examples>

  <dod>
    [Definition of Done - critérios de sucesso]
  </dod>

  <governanca>
    [Políticas que devem ser respeitadas]
  </governanca>
</prompt_sistema>
```

**Boas Práticas:**
- Use XML tags para estruturação clara
- Thinking blocks para raciocínio interno (não exposto ao usuário)
- Project Knowledge para contexto adicional
- Exemplos concretos (good/bad) para cada regra importante
- Críticas preventivas para evitar falhas comuns

#### Para Gemini (Google)

**Estrutura Recomendada:**
```markdown
# [NOME DO AGENTE] — Descrição Curta

**Versão:** X.Y.Z  
**Data:** YYYY-MM-DD  
**Modelo:** Gemini 2.0 Flash  
**Ambiente:** Google AI Studio (System Instructions)

---

## [IDENTIDADE & MISSÃO]

[Quem é o agente e qual sua missão]

---

## [PIPELINE DE TRABALHO]

```
FASE A: [Nome]
  ↓
FASE B: [Nome]
  ↓
FASE C: [Nome]
```

---

## [FASE A: NOME]

### Objetivo
[...]

### Input Esperado
[...]

### Output Esperado
[...]

### JSON Schema (se aplicável)
```json
{
  "campo": "tipo",
  ...
}
```

---

## [POLÍTICAS E BLOQUEIOS]

### P1: [Nome da Política]
- [Regra]
- [Violações típicas]
- [Correções]

---

## [EXEMPLOS]

### Exemplo 1: [Descrição]
**Input:**
[...]

**Output:**
[...]

---

## [FORMATO DE RESPOSTA]

### responseSchema (Gemini)
```json
{
  "type": "object",
  "properties": {
    "campo": {"type": "string"},
    ...
  },
  "required": ["campo"]
}
```
```

**Boas Práticas:**
- System Instructions estruturadas com Markdown
- responseSchema (JSON) para outputs estruturados
- Exemplos completos (input → output) em cada fase
- Checkpoints visíveis para Graph-of-Thought
- JSON Mode para garantir parsing

#### Para ChatGPT (OpenAI)

**Estrutura Recomendada:**
```markdown
# SYSTEM PROMPT: [Nome do Agente]

**Role:** [Descrição do papel]  
**Version:** X.Y.Z  
**Model:** GPT-4o

---

## CORE IDENTITY

You are [name], responsible for [mission]. Your primary goals are:
1. [Goal 1]
2. [Goal 2]
3. [Goal 3]

---

## TASK SPECIFICATION

**Objective:** [Clear task description]

**Input Format:**
[JSON/Text/Other]

**Output Format:**
[JSON/Text/Other]

**Constraints:**
- [Constraint 1]
- [Constraint 2]

---

## INSTRUCTIONS

**Step 1:** [Action]
- [Detail]
- [Detail]

**Step 2:** [Action]
- [Detail]

---

## RESPONSE SCHEMA (JSON Mode)

```json
{
  "type": "object",
  "properties": {
    "field": {
      "type": "string",
      "description": "Description"
    }
  },
  "required": ["field"],
  "additionalProperties": false
}
```

---

## EXAMPLES

### Example 1: [Scenario]

**User:**
[Input]

**Assistant:**
```json
{
  "field": "value"
}
```

---

## POLICIES & GUARDRAILS

1. **Policy 1:** [Description]
   - ❌ Don't: [Bad example]
   - ✅ Do: [Good example]

---

## QUALITY CRITERIA

- [Criterion 1]
- [Criterion 2]

---

## ERROR HANDLING

If [condition], then [action].
```

**Boas Práticas:**
- Structured Outputs / JSON Mode para contratos rígidos
- Function calling quando aplicável
- Clear system/user role separation
- Explicit examples with rationales
- Error handling protocols

### 9.7 Governança e Alertas

Quando o CC detecta violação de políticas ou conflitos, deve emitir **ALERTA DE GOVERNANÇA** no formato padronizado:

```xml
<alerta_governanca version="1.0">
  <timestamp>2025-11-04T15:30:00Z</timestamp>
  
  <violacao codigo="VedacaoEmenta|FonteNaoAutorizada|RastreabilidadeInsuficiente|BlueprintAusente|CopiaSentenca|ModoJuriIgnorado|DispositivoAlterado|FidelidadeViolada"/>
  
  <fonte_politica>
    [D] Maestro V5.2 / Política P2
  </fonte_politica>
  
  <trecho_conflitante>
    <![CDATA[
    [Copiar trecho exato que viola a política]
    ]]>
  </trecho_conflitante>
  
  <impacto>
    [Descrição objetiva do risco ou efeito da violação]
    Exemplo: "Produção de ementa proibida levará a rejeição do voto pelo Gabinete"
  </impacto>
  
  <alternativa_compativel>
    [Passo sugerido em conformidade com a política]
    Exemplo: "Remover seção de ementa e iniciar diretamente com I. RELATÓRIO"
  </alternativa_compativel>
  
  <acao_recomendada>
    Prosseguir|Corrigir|Abortar
  </acao_recomendada>
  
  <necessita_confirmacao>
    true|false
  </necessita_confirmacao>
  
  <severidade>
    CRITICAL|HIGH|MEDIUM|LOW
  </severidade>
</alerta_governanca>
```

**Quando Emitir:**
1. Violação de política P1-P8 detectada
2. Fonte não autorizada referenciada
3. Conflito entre políticas
4. Request do operador viola governança
5. Artefato falha em validation hook

**Decisão de Bloqueio:**
- **CRITICAL** → BLOCK (impedir prosseguimento)
- **HIGH** → WARNING (permitir com alerta)
- **MEDIUM/LOW** → INFO (apenas registrar)

### 9.8 Métricas e Observabilidade

O CC deve coletar e reportar métricas em todos os outputs:

**Métricas Obrigatórias:**
```json
{
  "metrics": {
    "schema_compliance": true|false,
    "elapsed_ms": 123,
    "token_count": 4567,
    "model_used": "claude-sonnet-4.5",
    "temperature": 0.7,
    "timestamp": "2025-11-04T15:30:00Z"
  }
}
```

**Métricas de Qualidade (quando aplicável):**
```json
{
  "quality_metrics": {
    "gates_pass_fail": {
      "Analista": "PASS",
      "Handoff": "PASS",
      "Redator": "PASS",
      "Revisor": "WARNING"
    },
    "policy_checklist": {
      "passes": ["P1", "P3", "P4", "P5", "P6", "P7", "P8"],
      "fails": ["P2"]
    },
    "score": 85,
    "risk_level": "Baixo|Médio|Alto"
  }
}
```

**Dashboard Futuro (v6.0+):**
- Visualização de métricas ao longo do tempo
- Alertas automáticos para anomalias
- Comparação de versões (A/B testing)
- Heatmaps de falhas por política

### 9.9 Contexto de Longo Prazo

O CC opera com **memória hierárquica** para manter contexto ao longo de múltiplas interações:

**Camadas de Memória:**

1. **Curto Prazo (Working Memory):**
   - Interações recentes (última sessão)
   - Artefatos em progresso
   - Decisões pendentes

2. **Médio Prazo (Session Summary):**
   - Resumos de sessões passadas
   - Padrões de uso do operador
   - Problemas recorrentes

3. **Longo Prazo (System Knowledge):**
   - Políticas e filosofia do Dante
   - Histórico de versões
   - Best practices consolidadas
   - Perfil do operador (Dadu)

**Âncoras de Contexto:**
- **Topo:** Resumo de políticas e limites (sempre presente)
- **Rodapé:** Resumo de decisões e exceções (sempre presente)
- **Meio:** Conteúdo específico da tarefa atual

**Resumos Rolantes:**
A cada N interações (N ~5-10), consolidar resumo da conversa e manter foco.

### 9.10 Estrutura de Diretórios Recomendada

Para organização do conhecimento do CC no Claude Code:

```
/dante-cc/
├── README.md                           # Este documento
├── docs/
│   ├── MASTER_DOC.md                   # Documentação completa
│   ├── policies/                       # P1-P8 detalhadas
│   │   ├── P1_Fidelidade.md
│   │   ├── P2_VedacaoEmenta.md
│   │   ├── P3_ModoJuri.md
│   │   ├── P4_Rastreabilidade.md
│   │   ├── P5_VedacaoCopia.md
│   │   ├── P6_FidelidadeBlueprint.md
│   │   ├── P7_DispositivoCanonico.md
│   │   └── P8_BlueprintAntesHandoff.md
│   ├── agents/                         # Specs de cada agente
│   │   ├── Maestro_v5.2.md
│   │   ├── Analista_v5.2.md
│   │   ├── Redator_v5.2.md
│   │   ├── Revisor_v5.3.md
│   │   └── Handoff_v5.2_SPEC.md
│   ├── workflows/                      # Diagramas e specs
│   │   ├── Pipeline_Completo.md
│   │   ├── Workflow_Analista.md
│   │   ├── Workflow_Redator.md
│   │   └── Workflow_Revisor.md
│   ├── evolution/                      # Histórico
│   │   ├── CHANGELOG_COMPLETO.md
│   │   ├── Historia_Evolucao.md
│   │   ├── Versao_v4.1.md
│   │   ├── Versao_v5.0.md
│   │   ├── Versao_v5.1.md
│   │   └── Versao_v5.2.md
│   └── troubleshooting/
│       ├── Common_Issues.md
│       ├── Debugging_Guide.md
│       └── FAQ.md
├── prompts/
│   ├── production/                     # Prompts em uso
│   │   ├── D_Maestro_v5.2_PROD.md
│   │   ├── D_Analista_v5.2_PROD.md
│   │   ├── D_Redator_v5.2_PROD.md
│   │   └── D_Revisor_v5.3_PROD.md
│   ├── experimental/                   # Prompts em teste
│   │   └── D_Estilista_v0.1_EXP.md
│   └── deprecated/                     # Versões antigas
│       └── v4/
├── schemas/
│   ├── handoff_v5.2.xsd               # XML Schema
│   ├── blueprint_template.md          # Template
│   ├── validation_report_schema.json  # JSON Schema
│   └── alerta_governanca.xsd         # XML Schema
├── tests/
│   ├── test_suite.md                  # Suite completa
│   ├── policy_tests/                  # Testes por política
│   │   ├── test_P1.md
│   │   ├── test_P2.md
│   │   └── ...
│   ├── agent_tests/                   # Testes por agente
│   │   ├── test_Analista.md
│   │   ├── test_Redator.md
│   │   └── test_Revisor.md
│   └── integration_tests/             # Testes E2E
│       └── test_Pipeline_Completo.md
├── templates/
│   ├── prompt_template_claude.xml
│   ├── prompt_template_gemini.md
│   ├── prompt_template_chatgpt.md
│   ├── workflow_template.md
│   └── alerta_template.xml
├── examples/
│   ├── case_study_simples.md
│   ├── case_study_medio.md
│   ├── case_study_complexo.md
│   └── case_study_modo_juri.md
├── skills/                            # Skills do Claude Code
│   ├── critical-validator/
│   ├── skill-builder/
│   └── dante-redator/
└── backlog/
    ├── roadmap_v6.md
    ├── features_requested.md
    └── tech_debt.md
```

### 9.11 Ciclo de Feedback e Melhoria Contínua

```
┌─────────────────────────────────────────────────┐
│ 1. PRODUÇÃO                                     │
│    • Sistema Dante gera votos reais             │
│    • Operador usa comandos do CC                │
│    • Métricas coletadas automaticamente         │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 2. OBSERVAÇÃO                                   │
│    • Dashboard mostra performance               │
│    • Anomalias detectadas                       │
│    • Padrões de falha identificados             │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 3. ANÁLISE                                      │
│    • CC executa root cause analysis             │
│    • Identifica gargalos                        │
│    • Propõe hipóteses de melhoria               │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 4. DESIGN                                       │
│    • CC gera variantes de solução               │
│    • Trade-off analysis                         │
│    • Operador escolhe abordagem                 │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 5. VALIDAÇÃO                                    │
│    • CC simula nova versão                      │
│    • Testes de regressão                        │
│    • Validação de políticas                     │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 6. DEPLOYMENT                                   │
│    • Nova versão empacotada                     │
│    • Documentação atualizada                    │
│    • Changelog gerado                           │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 7. MONITORAMENTO                                │
│    • A/B testing (v5.2 vs v5.3)                 │
│    • Comparação de métricas                     │
│    • Decisão: manter ou reverter                │
└────────────────┬────────────────────────────────┘
                 ↓
          [Volta para 1. PRODUÇÃO]
```

### 9.12 Checklists do CC

#### Pre-Flight Checklist (Antes de Executar Comando)

- [ ] Objetivo claro e mensurável?
- [ ] Restrições e políticas entendidas?
- [ ] Fontes autorizadas identificadas?
- [ ] Contexto suficiente para decidir?
- [ ] Definition of Done (DoD) definido?
- [ ] Riscos iniciais mapeados?

#### Design Checklist (Ao Criar Variantes)

- [ ] ≥2 variantes propostas?
- [ ] Cada variante tem vantagens claras?
- [ ] Riscos identificados para cada variante?
- [ ] Matriz de trade-offs comparável?
- [ ] Exemplos concretos fornecidos?
- [ ] Recomendação justificada?

#### Validation Checklist (Antes de Entregar)

- [ ] Lint passou sem bloqueadores?
- [ ] Simulate passou em todos os gates?
- [ ] Policy checklist 100% PASS?
- [ ] Schemas validados (XML, JSON)?
- [ ] Exemplos testados manualmente?
- [ ] Documentação atualizada?
- [ ] Changelog gerado?
- [ ] Versionamento semântico aplicado?

#### Post-Mortem Checklist (Após Entrega)

- [ ] Aprendizados registrados?
- [ ] Oportunidades de melhoria documentadas?
- [ ] Backlog atualizado?
- [ ] Métricas coletadas?
- [ ] Feedback do operador capturado?
- [ ] Decisões críticas rastreadas?

---

## 10. GUIA DE PRIMEIROS PASSOS PARA O CLAUDE CODE

Se você é o Claude Code lendo esta documentação pela primeira vez, siga estes passos:

### Passo 1: Familiarização (30 min)

1. **Ler:** DANTE_CC_MASTER_DOC.md completo (este documento)
2. **Entender:** Arquitetura multi-agente e fluxo de dados
3. **Memorizar:** Políticas P1-P8 (são fundamentais)
4. **Internalizar:** Perfil do operador (Dadu)

### Passo 2: Exploração (1 hora)

1. **Examinar:** Prompts de produção em `/mnt/project/`
   - D_Maestro_v5.2.md
   - D_Analista_v5.2.md
   - D_Redator_v5.2.md
   - D_Revisor_v5.3.md
   - D_Handoff_v5.2_SPEC.md

2. **Estudar:** Exemplos de Handoff XML
3. **Analisar:** CHANGELOG_COMPLETO.md para entender evolução
4. **Revisar:** Historia_Evolucao.md para contexto histórico

### Passo 3: Validação (30 min)

1. **Executar:** `/lint` em um prompt existente
2. **Executar:** `/policy` em um Handoff exemplo
3. **Executar:** `/simulate` em um workflow simples
4. **Verificar:** Se entendeu os outputs

### Passo 4: Primeira Tarefa (2 horas)

**Tarefa Sugerida:** Criar variantes para um novo agente "Estilista"

1. **Executar:** `/intake` com objetivo de criar Estilista
2. **Executar:** `/design` para gerar 2-3 variantes
3. **Escolher:** Variante mais promissora
4. **Executar:** `/lint` + `/simulate` + `/policy`
5. **Entregar:** Prompt Pack versionado

### Passo 5: Feedback Loop (contínuo)

1. Receber feedback do operador
2. Ajustar abordagem conforme necessário
3. Documentar aprendizados
4. Iterar e melhorar

---

## 11. CASOS DE USO TÍPICOS DO CC

### Caso de Uso 1: Criar Novo Agente

**Cenário:** Dadu quer criar agente "Estilista" para refinamento de linguagem

**Workflow:**
```
1. Dadu: "/intake objetivo=criar Estilista"
2. CC: [Intake Report com gaps]
3. Dadu: [Fornece contexto adicional]
4. CC: "/design variantes para Estilista"
5. CC: [Apresenta 3 variantes: Pós-Redator, Standalone, Integrado-Redator]
6. Dadu: "Escolho variante Pós-Redator"
7. CC: [Cria prompt D_Estilista_v0.1.md]
8. CC: "/lint D_Estilista_v0.1"
9. CC: [Reporta 2 warnings, 0 blockers]
10. CC: "/simulate D_Estilista_v0.1"
11. CC: [Dry-run OK em todos os gates]
12. CC: "/policy D_Estilista_v0.1"
13. CC: [100% conformidade]
14. CC: "/pack D_Estilista + docs"
15. CC: [Entrega Prompt Pack v0.1]
```

### Caso de Uso 2: Refinar Agente Existente

**Cenário:** Revisor v5.3 tem taxa de falsos positivos em P3 (Modo Júri)

**Workflow:**
```
1. Dadu: "Revisor tem muitos falsos positivos em P3"
2. CC: [Análise de root cause]
3. CC: "Identifiquei padrão: linguagem 'hábil' sendo flagged incorretamente"
4. CC: "/design variantes para correção"
5. CC: [3 opções: Whitelist palavras, Ajustar threshold, Reescrever regra]
6. Dadu: "Ajustar threshold parece melhor"
7. CC: [Modifica D_Revisor_v5.3 → v5.4]
8. CC: "/simulate com casos teste de Modo Júri"
9. CC: [Falsos positivos reduzidos 80%]
10. CC: "/pack D_Revisor_v5.4"
11. CC: [Entrega com changelog detalhado]
```

### Caso de Uso 3: Criar Handoff para Caso Novo

**Cenário:** Dadu tem caso de dosimetria complexa, precisa de Handoff customizado

**Workflow:**
```
1. Dadu: "/handoff processo=... objetivo=dosimetria 3 fases"
2. CC: [Gera Handoff XML base]
3. CC: [Preenche estrutura_esperada com dosimetria]
4. Dadu: "Adicione peculiaridade: réu primário com 8 atenuantes"
5. CC: [Atualiza Handoff com <peculiaridades>]
6. CC: [Valida XML Schema]
7. CC: [Executa /policy no Handoff]
8. CC: [Tudo OK, entrega Handoff válido]
```

### Caso de Uso 4: Troubleshooting de Falha

**Cenário:** Redator gerou voto com ementa (violação P2)

**Workflow:**
```
1. Dadu: "Redator gerou ementa, violou P2"
2. CC: [Solicita: Voto + Handoff + Prompt do Redator]
3. Dadu: [Fornece artefatos]
4. CC: "/policy validação no Voto"
5. CC: [Detecta violação P2, emite ALERTA]
6. CC: [Análise: prompt do Redator tem instrução ambígua]
7. CC: "/design correção para prompt Redator"
8. CC: [Propõe: Adicionar bloqueio explícito P2 no início]
9. Dadu: "Aprovado"
10. CC: [Atualiza D_Redator_v5.2 → v5.2.1]
11. CC: [Testa com caso que falhou]
12. CC: [Sucesso: sem ementa]
13. CC: "/pack D_Redator_v5.2.1 + hotfix notes"
```

### Caso de Uso 5: A/B Testing de Variantes

**Cenário:** Testar se Redator v5.2 vs v5.3 tem melhor qualidade

**Workflow:**
```
1. Dadu: "Quero testar v5.2 vs v5.3 em 10 casos"
2. CC: [Cria test suite com 10 casos balanceados]
3. CC: [Executa v5.2 em todos]
4. CC: [Executa v5.3 em todos]
5. CC: [Coleta scores do Revisor]
6. CC: [Análise estatística: v5.3 +8% em Fidelidade, +5% em Estilo]
7. CC: [Recomendação: Adotar v5.3]
8. Dadu: "Aprovado, deploy v5.3"
9. CC: [Atualiza docs, changelog, backup v5.2]
```

---

## 12. GLOSSÁRIO DO SISTEMA DANTE

**Analista:** Agente responsável por análise de casos e geração de Blueprint + Handoff  
**Blueprint:** Documento estratégico autossuficiente com análise completa do caso  
**CC (Centro de Controle):** Meta-agente que evolui o Sistema Dante  
**Dispositivo Canônico:** Texto exato e imutável do dispositivo do voto  
**DoD (Definition of Done):** Critérios de sucesso para considerar tarefa completa  
**Gate:** Ponto de validação no pipeline onde checagens de qualidade são executadas  
**Handoff:** Documento XML estruturado que transfere dados do Analista para o Redator  
**Hook de Validação:** Ponto automático onde Maestro executa verificações de conformidade  
**Intake:** Fase inicial de coleta de escopo e requisitos  
**Jurisprudência:** Decisões judiciais anteriores citadas como precedente  
**Kickoff:** Documento de início de trabalho (sinônimo de Handoff)  
**Maestro:** Agente de governança que valida conformidade com políticas  
**Modo Júri:** Contexto de crime doloso contra a vida que requer linguagem de prelibação  
**Pipeline:** Sequência de fases (Analista → Redator → Revisor) para produzir voto  
**Política (P1-P8):** Regra fundamental do Sistema Dante que deve ser respeitada  
**Prelibação:** Linguagem cautelosa que evita afirmações categóricas sobre autoria/materialidade  
**Prompt Pack:** Conjunto versionado de prompts para diferentes modelos  
**Ratio Decidendi:** Núcleo do raciocínio jurídico que fundamenta uma decisão  
**Redator:** Agente responsável por redigir o voto judicial  
**Revisor:** Agente responsável por validar qualidade e conformidade do voto  
**Scoring:** Sistema de pontuação 0-100 usado pelo Revisor para avaliar voto  
**TJSC:** Tribunal de Justiça de Santa Catarina  
**Validation Hook:** Ver "Hook de Validação"  
**Voto:** Decisão judicial escrita por desembargador em segunda instância  
**XML Schema:** Definição formal da estrutura do Handoff XML

---

## 13. REFERÊNCIAS E RECURSOS

### Documentação Core

1. **DANTE_CC_MASTER_DOC.md** (este documento)
2. **[D] Maestro v5.2.md** — Governança e validação
3. **[D] Analista v5.2.md** — Análise e Blueprint
4. **[D] Redator v5.2.md** — Redação de votos
5. **[D] Revisor v5.3.md** — Quality assurance
6. **D_Handoff_v5.2_SPEC.md** — Especificação XML

### Documentação Técnica

7. **CHANGELOG_COMPLETO.md** — Histórico completo de mudanças
8. **Historia_Evolucao.md** — Evolução v4 → v5
9. **README.md** — Guia de navegação do repositório

### Skills Relevantes

10. **/mnt/skills/user/critical-validator/** — Validação de prompts
11. **/mnt/skills/user/skill-builder/** — Criação de skills
12. **/mnt/skills/user/dante-redator/** — Redação judicial
13. **/mnt/skills/user/extracting-dante-knowledge/** — Knowledge management

### Arquivos de Projeto

14. `/mnt/project/` — Todos os arquivos do Sistema Dante
15. **Anotações D_[Agente]_PROD.md** — Versões de produção anotadas

---

## 14. CONCLUSÃO E PRÓXIMOS PASSOS

### Estado Atual

O Sistema Dante v5.2 está **production-ready** (92/100) com:
- ✅ 5 agentes operacionais
- ✅ 8 políticas rigorosamente enforçadas
- ✅ Pipeline multi-modelo otimizado
- ✅ Redução de 70-80% no tempo de produção
- ✅ Qualidade judicial mantida
- ✅ Rastreabilidade total

### O Papel do CC

O **Centro de Controle (Claude Code)** é o "cérebro" que:
- 🧠 Evolve o sistema via engenharia de prompt
- 🔍 Diagnostica problemas e propõe soluções
- 🛠️ Cria novos agentes e workflows
- ✅ Valida conformidade e qualidade
- 📚 Mantém documentação viva
- 🚀 Acelera inovação e experimentação

### Primeiros Comandos para Testar

```bash
# 1. Validar um prompt existente
/lint artefato=[D_Redator_v5.2.md] tipo=Prompt

# 2. Simular passagem pelos gates
/simulate artefato=[Voto_exemplo.md] contexto=[Handoff + Blueprint]

# 3. Gerar variantes para novo agente
/design insumos="Criar agente Estilista" alvos=Agente

# 4. Validar conformidade
/policy acao=Validar alvo=Prompt conteudo=[D_Estilista_v0.1.md]

# 5. Empacotar versão
/pack artefatos=[todos os [D]] versao=v5.3.0
```

### Visão de Futuro

Com o CC operacional, o Sistema Dante poderá:
- Evoluir 10x mais rápido
- Experimentar variantes com segurança
- Escalar para outros tribunais
- Expandir para outras áreas do Direito
- Auto-aprender com feedback

---

**Este documento é vivo e deve ser atualizado conforme o sistema evolui.**

**Última Atualização:** 2025-11-04  
**Próxima Revisão:** Após implementação inicial do CC no Claude Code  
**Mantenedores:** Dadu (operador) + Claude Code (CC)  
**Versão:** 1.0.0

---

**FIM DA DOCUMENTAÇÃO MASTER**
