# CENTRO DE CONTROLE DO SISTEMA DANTE (CC)
## Documentação Master para Claude Code

**Versão:** 1.0.0  
**Data:** 2025-11-04  
**Status:** Foundation Document  
**Público:** Claude Code AI Assistant

---

## ÍNDICE EXECUTIVO

1. [Visão Geral do Sistema Dante](#1-visão-geral)
2. [Arquitetura Multi-Agente](#2-arquitetura-multi-agente)
3. [Políticas e Governança](#3-políticas-e-governança)
4. [Pipeline e Workflow](#4-pipeline-e-workflow)
5. [Perfil do Operador (Dadu)](#5-perfil-do-operador)
6. [Filosofia e Design Principles](#6-filosofia-e-design-principles)
7. [Tecnologia e Implementação](#7-tecnologia-e-implementação)
8. [Evolução e Roadmap](#8-evolução-e-roadmap)
9. [Plano de Infraestrutura CC](#9-plano-de-infraestrutura-cc)

---

## 1. VISÃO GERAL

### 1.1 O que é o Sistema Dante?

O **Sistema Dante** é uma plataforma de engenharia de documentos jurídicos alimentada por IA multi-modelo, projetada para tribunais brasileiros de segunda instância. Automatiza a produção de votos judiciais (decisões judiciais) para apelações criminais, transformando análise jurídica complexa em documentos estruturados de alta qualidade.

**Problema Resolvido:**
- Desembargadores gastam 6-12 horas por voto
- Alta carga processual (100+ casos/mês por desembargador)
- Necessidade de conformidade rigorosa com padrões jurisprudenciais
- Risco de erros factuais ou citações incorretas

**Solução:**
- Redução de tempo de composição em ~70% (de 8h para 2-3h)
- Manutenção de padrões de qualidade judicial
- Conformidade automática com políticas e formatação
- Rastreabilidade total de provas e citações

### 1.2 Contexto Institucional

**Tribunal Alvo:** Tribunal de Justiça de Santa Catarina (TJSC)  
**Instância:** Segunda instância (recursos de apelação)  
**Domínio:** Direito Penal brasileiro  
**Jurisdição:** Crimes julgados em primeira instância

**Tipos de Peças Produzidas:**
- Votos judiciais (decisões de desembargadores)
- Acórdãos de apelação criminal
- Decisões monocráticas

### 1.3 Status Atual

**Versão em Produção:** v5.2 (outubro 2025)  
**Score Production-Ready:** 91/100  
**Agentes Operacionais:** 5 (Maestro, Analista, Redator, Revisor, Handoff)  
**Modelos AI Utilizados:** 
- Google Gemini 2.0 Flash (Analista, Revisor)
- Claude Sonnet 4.5 (Redator)
- Multi-modelo (Maestro - agnóstico)

---

## 2. ARQUITETURA MULTI-AGENTE

### 2.1 Visão de Componentes

```
┌──────────────────────────────────────────────────────────────┐
│                    SISTEMA DANTE v5.2                         │
│                                                                │
│  ┌─────────────┐                                              │
│  │  MAESTRO    │  ← Camada de Governança (silenciosa)        │
│  │ (Políticas) │     Validation Hooks automáticos             │
│  └──────┬──────┘                                              │
│         │ valida                                              │
│         ↓                                                      │
│  ┌──────────────────────────────────────────────┐            │
│  │              PIPELINE PRINCIPAL               │            │
│  │                                                │            │
│  │  1. ANALISTA (Gemini) → 2. HANDOFF (XML)     │            │
│  │           ↓                      ↓             │            │
│  │     Blueprint.md          kickoff_redator     │            │
│  │                                 ↓              │            │
│  │  3. REDATOR (Claude) → 4. REVISOR (Gemini)   │            │
│  │           ↓                      ↓             │            │
│  │      Voto.md              Validation Report   │            │
│  └──────────────────────────────────────────────┘            │
│                                                                │
│  Operador Humano: Dadu                                        │
│  ↕ (comandos, aprovações, ajustes iterativos)                │
└──────────────────────────────────────────────────────────────┘
```

### 2.2 Agentes Detalhados

#### 2.2.1 [D] MAESTRO — Camada de Governança

**Papel:** Garantir conformidade com todas as políticas do sistema  
**Modelo:** Agnóstico (Claude/Gemini/ChatGPT)  
**Modo de Operação:** Silencioso e proativo  

**Responsabilidades:**
- Validar artefatos em pontos críticos via hooks automáticos
- Bloquear violações CRITICAL antes de propagarem
- Facilitar decisões via matriz de trade-offs quando políticas conflitam
- Manter rastreabilidade de decisões e exceções
- Emitir alertas de governança quando necessário

**Hooks de Validação:**
- `ON_ARTIFACT_GENERATED`: Após Blueprint/Voto
- `ON_HANDOFF_CREATED`: Antes de passar ao Redator
- `ON_REDATOR_INVOKED`: Validação pré-redação

**Quando se Manifesta:**
1. Chamado explicitamente via `/policy`
2. Detecta violação CRITICAL
3. Acionado por validation hooks

**Documentos:**
- `[D] Maestro v5.2.md` (prompt principal)
- `Anotações D_Maestro_v5.2_PROD.md` (versão de produção)

#### 2.2.2 [D] ANALISTA — Case Analysis & Blueprint Engine

**Papel:** Analisar casos jurídicos e estruturar estratégia de voto  
**Modelo:** Google Gemini 2.0 Flash  
**Ambiente:** Google AI Studio (System Instructions)  

**Responsabilidades:**
1. **Intake:** Receber autos e kickoff do operador
2. **Catalogação:** Criar IDs únicos para provas (P01, P02, P03...)
3. **Mapeamento:** Identificar teses, argumentos, contra-argumentos
4. **Diálogo Estratégico:** 3-5 rodadas com operador antes de Blueprint
5. **Blueprint:** Gerar documento autossuficiente com estratégia completa
6. **Handoff XML:** Criar contrato de interface estruturado para Redator

**Pipeline Interno (FASE A → B → C → D):**
```
FASE A: INTAKE
  ├─ Receber documentos processuais
  ├─ Validar kickoff estruturado
  └─ Catalogar provas iniciais

FASE B: ANÁLISE & DIÁLOGO
  ├─ Análise preliminar (JSON estruturado)
  ├─ Prompt de jurisprudência (automático)
  ├─ Diálogo estratégico (3-5 rodadas)
  └─ Alinhamento de ratio decidendi

FASE C: BLUEPRINT
  ├─ Documento markdown autossuficiente
  ├─ Máxima informatividade
  └─ Validação pré-handoff

FASE D: HANDOFF XML
  ├─ XML Schema v5.2 compliant
  ├─ Economia inteligente de tokens
  └─ Campos obrigatórios + opcionais
```

**Outputs Principais:**
- **Blueprint.md:** Estratégia completa, autossuficiente
- **Handoff XML:** Contrato estruturado (kickoff_redator)

**Documentos:**
- `[D] Analista v5.2.md` (prompt principal)
- `Anotações D_Analista_v5.2_.md` (versão de produção)

#### 2.2.3 [D] HANDOFF — Interface Specification

**Papel:** Especificação técnica do contrato entre Analista e Redator  
**Tipo:** Documento técnico (não é agente executável)  
**Formato:** XML Schema + Guidelines  

**Propósito:**
- Definir estrutura XML rigorosa para transferência de dados
- Garantir economia inteligente de tokens (campos opcionais)
- Especificar campos obrigatórios vs. opcionais
- Documentar estrutura esperada do voto

**Campos Obrigatórios:**
- `<processo>`: Número, órgão, vara
- `<tipo_peca>`: Acórdão, sentença, voto
- `<estrutura_esperada>`: **NOVO v5.2** — Sinaliza estrutura hierárquica
- `<fundamentos>`: Contexto, provas, teses, ratio
- `<escopo>`: Objetivo, estilo, extensão
- `<dispositivo_canonico>`: Texto exato, imutável
- `<nao_fazer>`: Políticas críticas

**Campos Opcionais** (Economia Inteligente):
- `<banner_modo_juri>`: Se crime doloso contra a vida
- `<peculiaridades>`: Se caso tem aspectos atípicos
- `<sensibilidades>`: Se requer cuidados especiais
- `<anexos>`: Se há documentos adicionais

**Trade-off de Tokens:**
- Caso simples: ~600 tokens (-78% vs. v4.1)
- Caso médio: ~1100 tokens (-60% vs. v4.1)
- Caso complexo: ~1800 tokens (-35% vs. v4.1, MAS contexto completo)

**Documentos:**
- `[D] Handoff v5.2_SPEC.md` (especificação técnica)
- `Anotações D_Handoff_v5.2_SPEC.md` (versão de produção)
- `D_Handoff_v5.2_SPEC.md` (schema e exemplos)

#### 2.2.4 [D] REDATOR — Judicial Opinion Writer

**Papel:** Redigir votos judiciais de alta qualidade  
**Modelo:** Claude Sonnet 4.5  
**Ambiente:** Claude.ai Projects (Project: "[D] Dante V5")  

**Responsabilidades:**
1. **Parse:** Interpretar Handoff XML + Blueprint
2. **Planejamento:** Estrutura hierárquica do voto
3. **Redação:** Voto completo (Relatório + Voto + Dispositivo)
4. **Conformidade:** Respeitar todas as políticas Dante
5. **Output Bipartido:**
   - **Chat:** Metadados em prosa (tipo, estimativas, contexto)
   - **Artifact:** Voto completo em markdown

**Pipeline Interno:**
```
INPUT: Handoff XML + Blueprint
  ↓
PARSING & PLANNING (thinking block)
  ↓
OUTPUT 1: Metadados (chat)
  ├─ Tipo de peça
  ├─ Complexidade estimada
  ├─ Tempo estimado
  └─ Alertas e considerações
  ↓
OUTPUT 2: Voto (artifact markdown)
  ├─ I. RELATÓRIO
  ├─ II. VOTO (hierárquico)
  └─ III. DISPOSITIVO
  ↓
ITERAÇÃO: Ajustes com operador
```

**Estrutura Obrigatória (Tripartida):**
```markdown
I. RELATÓRIO
   [Síntese processual: 200-300 palavras]

II. VOTO
   [SE tem_preliminares:]
   1. PRELIMINARES
      1.1. [Primeira preliminar]
      1.2. [Segunda preliminar]
   
   [SEMPRE:]
   2. MÉRITO
      2.1. [Primeira tese]
         2.1.1. [Subtópico se necessário]
      2.2. [Segunda tese]
   
   [SE tem_dosimetria:]
   3. DOSIMETRIA
      3.1. Primeira fase — Pena-base
      3.2. Segunda fase — Agravantes/Atenuantes
      3.3. Terceira fase — Causas de aumento/diminuição
      3.4. Regime inicial

III. DISPOSITIVO
   [Texto EXATO do Handoff]
```

**Políticas Críticas Observadas:**
- P1: Fidelidade aos autos (IDs de prova)
- P2: Vedação de ementa (BLOQUEIO ABSOLUTO)
- P3: Modo Júri (linguagem de prelibação)
- P5: Vedação de cópia (paráfrase)
- P6: Fidelidade à Blueprint
- P7: Dispositivo canônico (imutável)

**Documentos:**
- `[D] Redator v5.2.md` (prompt principal)
- `Anotações D_Redator_v5.2_PROD.md` (versão de produção)
- `D_Redator_v5_3.md` (evolução para v5.3)

#### 2.2.5 [D] REVISOR — Quality Assurance & Validation

**Papel:** Validar qualidade do voto e fornecer feedback estruturado  
**Modelo:** Google Gemini 2.0 Flash  
**Ambiente:** Google AI Studio (System Instructions)  

**Responsabilidades:**
1. **Análise Adversarial:** Criticar trabalho do Redator antes de aprovar
2. **Scoring Multidimensional:** 0-100 em 5 dimensões
3. **Validação em 3 Camadas:** Estrutural, Estilística, Conformidade
4. **Feedback Estruturado:** Identificar falhas e sugerir correções
5. **Decisão Final:** PASS (≥80) ou FAIL (<80)

**Dimensões de Scoring:**
1. **Fidelidade (30%):** Rastreabilidade de fatos, ausência de invenção
2. **Conformidade (25%):** Respeito às políticas Dante
3. **Jurisprudência (20%):** Qualidade e adequação das citações
4. **Estrutura (15%):** Organização tripartida, hierarquia, coesão
5. **Estilo (10%):** Tom, voz, cadência, naturalidade

**Pipeline de Validação:**
```
INPUT: Voto + Handoff + Blueprint
  ↓
CAMADA 1: ESTRUTURAL
  ├─ Tripartite structure presente?
  ├─ Numeração hierárquica correta?
  ├─ Dispositivo canônico preservado?
  └─ Seções obrigatórias presentes?
  ↓
CAMADA 2: ESTILÍSTICA
  ├─ Linguagem natural e fluente?
  ├─ Modo Júri respeitado (se aplicável)?
  ├─ Conectivos variados?
  └─ Parágrafos bem dimensionados?
  ↓
CAMADA 3: CONFORMIDADE
  ├─ Fidelidade aos autos (P1)?
  ├─ Ausência de ementa (P2)?
  ├─ Rastreabilidade jurisprudencial (P4)?
  ├─ Sem cópia integral (P5)?
  └─ Blueprint respeitada (P6)?
  ↓
OUTPUT: Validation Report (JSON + Prosa)
  ├─ Score total (0-100)
  ├─ Scores por dimensão
  ├─ Falhas identificadas
  ├─ Sugestões de correção
  └─ Decisão: PASS/FAIL
```

**Critério de Aprovação:**
- Score ≥ 80: **PASS** (aprovado para entrega)
- Score < 80: **FAIL** (retorna ao Redator com feedback)

**Output Especial — Modo Reescrita:**
- Gera **rascunho com tracking** (negrito para adições, **[...]** para supressões)
- Operador analisa e limpa manualmente depois
- Não é versão final, é sugestão estruturada

**Documentos:**
- `D_Revisor_v5.3.md` (prompt principal)
- Informações em memória sobre v5.3

### 2.3 Fluxo de Dados e Artefatos

```
[Operador: Dadu] 
  │
  │ Upload: Sentença, Apelação, Contrarrazões, Laudos, Transcrições
  ↓
┌──────────────────────────────────────────────┐
│ [D] ANALISTA (Gemini 2.0 Flash)             │
│                                              │
│ FASE A: Intake                               │
│   • Catalogação de provas (P01, P02...)     │
│   • Resumo estruturado                       │
│                                              │
│ FASE B: Diálogo Estratégico                 │
│   • Análise preliminar                       │
│   • Prompt jurisprudencial (auto)           │
│   • 3-5 rodadas com operador                │
│   • Alinhamento de ratio decidendi          │
│                                              │
│ FASE C: Blueprint                            │
│   • Documento .md autossuficiente            │
│   • Máxima informatividade                   │
│                                              │
│ FASE D: Handoff XML                          │
│   • Schema v5.2 compliant                    │
│   • Economia inteligente de tokens          │
└──────────────────────────────────────────────┘
  │
  │ Output: Blueprint.md + kickoff_redator.xml
  ↓
[VALIDATION HOOK: Maestro valida Blueprint + Handoff]
  │
  │ Se PASS:
  ↓
┌──────────────────────────────────────────────┐
│ [D] REDATOR (Claude Sonnet 4.5)             │
│ Ambiente: Claude.ai Projects                 │
│                                              │
│ • Parse Handoff XML                          │
│ • Planning (thinking block)                  │
│ • Output 1: Metadados (chat)                 │
│ • Output 2: Voto (artifact .md)              │
│   ├─ I. RELATÓRIO                            │
│   ├─ II. VOTO (hierárquico)                  │
│   └─ III. DISPOSITIVO                        │
└──────────────────────────────────────────────┘
  │
  │ Output: Voto_v1.md
  ↓
[VALIDATION HOOK: Maestro valida Voto]
  │
  │ Se PASS ou WARNING:
  ↓
┌──────────────────────────────────────────────┐
│ [D] REVISOR (Gemini 2.0 Flash)              │
│                                              │
│ • Análise adversarial                        │
│ • Scoring multidimensional (0-100)          │
│ • Validação 3 camadas                        │
│ • Feedback estruturado                       │
└──────────────────────────────────────────────┘
  │
  │ Output: Validation_Report.json + Prosa
  ↓
[Decisão: Score ≥ 80?]
  │
  ├─ SIM → ✅ APROVADO
  │         └─> Entrega ao Operador
  │
  └─ NÃO → ❌ FAIL
            └─> Retorna ao Redator com feedback
                  └─> Ciclo de reescrita
```

---

## 3. POLÍTICAS E GOVERNANÇA

### 3.1 Sistema de Políticas (P1-P8)

O Sistema Dante opera sob **8 políticas fundamentais** que garantem conformidade jurídica, qualidade e rastreabilidade. Cada política tem:
- **ID único** (P1-P8)
- **Severidade** (CRITICAL, HIGH, MEDIUM, LOW)
- **Escopo** (componentes afetados)
- **Validation hook** (quando validar)
- **Remediação** (ação quando violada)

#### P1: FIDELIDADE AOS AUTOS

**ID:** P1  
**Severidade:** CRITICAL  
**Escopo:** Analista, Redator, Revisor  

**Regra:**  
Proibido inventar, inferir ou extrapolar fatos não presentes nos autos. Toda afirmação factual DEVE ter rastreabilidade a documento/evento identificado.

**Violações Típicas:**
- ❌ "A testemunha afirmou X" sem citar fonte (fls., evento, ID)
- ❌ "Ficou comprovado que Y" sem indicar prova específica
- ❌ Inferências apresentadas como fatos ("certamente", "obviamente")

**Correções:**
- ✅ "Segundo depoimento P02 (fls. 45), réu afirmou..."
- ✅ "Conforme laudo P05 (fls. 120-130), perito concluiu..."
- ✅ Toda afirmação factual deve ter ID de prova ou referência

**Remediação:** BLOCK + exigir correção com rastreabilidade

#### P2: VEDAÇÃO DE EMENTA

**ID:** P2  
**Severidade:** CRITICAL  
**Escopo:** Redator  

**Regra:**  
Proibido gerar ementa. Voto deve iniciar diretamente com "I. RELATÓRIO".

**Violações Típicas:**
- ❌ Qualquer seção rotulada "EMENTA"
- ❌ Texto resumido antes do Relatório
- ❌ Parágrafo inicial com resumo decisório

**Correções:**
- ✅ Iniciar diretamente com "I. RELATÓRIO"
- ✅ Sem texto antes da estrutura tripartida

**Remediação:** BLOCK + remover seção

#### P3: MODO JÚRI

**ID:** P3  
**Severidade:** HIGH  
**Escopo:** Analista, Redator  

**Regra:**  
Para crimes dolosos contra a vida (competência do Júri), usar **linguagem de prelibação**. Evitar afirmações categóricas sobre autoria/materialidade.

**Quando Aplicável:**
- Homicídio doloso (Art. 121 CP)
- Infanticídio (Art. 123 CP)
- Aborto provocado pela gestante ou com seu consentimento (Art. 124 CP)
- Induzimento, instigação ou auxílio a suicídio (Art. 122 CP)

**Linguagem Adequada:**
- ✅ "Há indícios de que..."
- ✅ "Elementos indicam..."
- ✅ "Segundo acusação..."
- ✅ "Aparenta configurar..."
- ✅ "Provas preliminares sugerem..."

**Linguagem Proibida:**
- ❌ "Réu matou vítima"
- ❌ "Autor do crime"
- ❌ "Ficou comprovado que cometeu"
- ❌ "É culpado de"

**Remediação:** WARNING + ajustar linguagem

#### P4: RASTREABILIDADE DE JURISPRUDÊNCIA

**ID:** P4  
**Severidade:** HIGH  
**Escopo:** Analista, Redator, Revisor  

**Regra:**  
Toda jurisprudência citada deve ter **identificação mínima**: Tribunal + Número do processo.  
**Identificação ideal**: Tribunal + Número + Relator + Data.

**Exemplos Corretos:**
- ✅ Mínimo: "STJ, REsp 1.234.567"
- ✅ Ideal: "STJ, REsp 1.234.567, Rel. Min. João Silva, j. 15/03/2024"

**Exemplos Incorretos:**
- ❌ "O STJ já decidiu que..."
- ❌ "Conforme jurisprudência consolidada..."
- ❌ "Em caso semelhante, o tribunal..."

**Remediação:** WARNING + adicionar identificação mínima

#### P5: VEDAÇÃO DE CÓPIA INTEGRAL

**ID:** P5  
**Severidade:** CRITICAL  
**Escopo:** Analista, Redator  

**Regra:**  
Proibido copiar integralmente sentenças, acórdãos ou petições. Permitido:
- Citações curtas entre aspas (máx. 2-3 linhas)
- Paráfrases substanciais
- Sínteses com linguagem própria

**Violações Típicas:**
- ❌ Copiar parágrafos inteiros de sentença
- ❌ Reproduzir fundamentação alheia sem paráfrase
- ❌ Usar blocos de texto >3 linhas sem aspas

**Correções:**
- ✅ Parafrasear com linguagem própria
- ✅ Citar trechos curtos entre aspas
- ✅ Sintetizar com palavras diferentes

**Remediação:** BLOCK + exigir paráfrase

#### P6: FIDELIDADE À BLUEPRINT

**ID:** P6  
**Severidade:** HIGH  
**Escopo:** Redator  

**Regra:**  
Redator deve seguir a linha argumentativa da Blueprint. Desvios significativos exigem consulta ao operador.

**Quando Permitir Desvios:**
- Operador autoriza explicitamente
- Erro factual detectado na Blueprint (P1 prevalece)

**Remediação:** WARNING + consultar operador antes de desviar

#### P7: DISPOSITIVO CANÔNICO

**ID:** P7  
**Severidade:** CRITICAL  
**Escopo:** Redator  

**Regra:**  
Dispositivo do voto deve ser EXATAMENTE o texto fornecido no Handoff XML. Zero alterações permitidas.

**Violações Típicas:**
- ❌ Parafrasear dispositivo
- ❌ Adicionar palavras
- ❌ Remover termos
- ❌ Alterar ordem

**Correções:**
- ✅ Copiar exato do `<dispositivo_canonico>` do Handoff

**Remediação:** BLOCK + restaurar texto original

#### P8: BLUEPRINT ANTES DE HANDOFF

**ID:** P8  
**Severidade:** HIGH  
**Escopo:** Analista  

**Regra:**  
Handoff XML só pode ser gerado APÓS Blueprint completo e aprovado.

**Violações Típicas:**
- ❌ Gerar Handoff sem Blueprint
- ❌ Pular fase de diálogo estratégico
- ❌ Gerar Handoff antes de validação

**Remediação:** BLOCK + exigir Blueprint primeiro

### 3.2 Validation Hooks Automáticos

**O que são?**  
Pontos de validação automática no pipeline onde o Maestro executa verificações de conformidade sem ser chamado explicitamente.

**Hook 1: ON_ARTIFACT_GENERATED**  
**Trigger:** Após geração de Blueprint ou Voto  
**Validações:** P1, P2, P4, P5, P6, P7

**Hook 2: ON_HANDOFF_CREATED**  
**Trigger:** Analista gera Handoff XML  
**Validações:** P3 (Modo Júri), P8 (Blueprint antes de Handoff), Schema XML

**Hook 3: ON_REDATOR_INVOKED**  
**Trigger:** Antes de Redator iniciar redação  
**Validações:** P8 (Handoff válido presente)

### 3.3 Matriz de Trade-offs

Quando duas políticas conflitam, o Maestro usa matriz de decisão:

| Cenário | Política A | Política B | Decisão |
|---------|-----------|-----------|---------|
| Crime doloso, prova categórica | P1 (Fidelidade) | P3 (Modo Júri) | **P3 prevalece** (linguagem cautelosa) |
| Blueprint sugere argumento, prova insuficiente | P6 (Blueprint) | P1 (Fidelidade) | **P1 prevalece** (fidelidade aos autos) |
| Operador autoriza override | Qualquer P | Autorização explícita | **Autorização prevalece** (registrar exceção) |

---

## 4. PIPELINE E WORKFLOW

### 4.1 Workflow Completo (Happy Path)

```
FASE 1: PREPARAÇÃO
──────────────────
[Dadu] 
  ├─ Coleta processo (PJE, eproc)
  ├─ Organiza documentos principais/acessórios
  ├─ Prepara kickoff estruturado
  └─ Upload para Gemini (Analista)

FASE 2: ANÁLISE E ESTRATÉGIA
────────────────────────────
[D] ANALISTA (Gemini)
  ├─ A. INTAKE
  │   ├─ Catalogação de provas (P01, P02, P03...)
  │   └─ Resumo estruturado (JSON)
  │
  ├─ B. DIÁLOGO ESTRATÉGICO (3-5 rodadas)
  │   ├─ Análise preliminar tese-a-tese
  │   ├─ Prompt jurisprudencial (automático)
  │   ├─ [Dadu] pesquisa jurisprudência (JusIA/manual)
  │   ├─ [Dadu] fornece jurisprudências relevantes
  │   ├─ Discussão de ratio decidendi
  │   └─ Alinhamento estratégico
  │
  ├─ C. BLUEPRINT
  │   ├─ Comando [Dadu]: "Gerar blueprint"
  │   ├─ Documento .md autossuficiente
  │   ├─ Máxima informatividade
  │   └─ [HOOK: Maestro valida Blueprint]
  │
  └─ D. HANDOFF XML
      ├─ Comando [Dadu]: "Gerar handoff"
      ├─ Schema v5.2 compliant
      ├─ Economia inteligente
      └─ [HOOK: Maestro valida Handoff]

FASE 3: REDAÇÃO
───────────────
[Dadu]
  ├─ Copia Handoff XML
  ├─ Abre Claude.ai Projects ("[D] Dante V5")
  └─ Cola Handoff no chat

[D] REDATOR (Claude)
  ├─ [HOOK: Maestro valida pré-redação]
  ├─ Parse do Handoff + Blueprint (thinking)
  ├─ Planning da estrutura (thinking)
  ├─ OUTPUT 1: Metadados (chat)
  │   ├─ Tipo de peça
  │   ├─ Complexidade estimada
  │   ├─ Tempo estimado
  │   └─ Alertas
  └─ OUTPUT 2: Voto (artifact .md)
      ├─ I. RELATÓRIO
      ├─ II. VOTO (hierárquico)
      └─ III. DISPOSITIVO

[Dadu] → Iteração (opcional)
  ├─ "Adicione jurisprudência X na Tese Y"
  ├─ "Reescreva Tese 2 com mais ênfase em Z"
  ├─ "Corrija numeração da Dosimetria"
  └─ Redator ajusta e gera nova versão

FASE 4: REVISÃO E VALIDAÇÃO
───────────────────────────
[Dadu]
  ├─ Copia Voto final
  ├─ Retorna ao Gemini (Revisor)
  └─ Fornece: Voto + Handoff + Blueprint

[D] REVISOR (Gemini)
  ├─ Análise adversarial
  ├─ Scoring multidimensional (0-100)
  ├─ Validação 3 camadas
  └─ OUTPUT: Validation Report
      ├─ Score total
      ├─ Scores por dimensão
      ├─ Falhas identificadas
      ├─ Sugestões de correção
      └─ Decisão: PASS (≥80) ou FAIL (<80)

[Decisão]
  ├─ Score ≥ 80: ✅ APROVADO
  │   └─> [Dadu] entrega ao Gabinete
  │
  └─ Score < 80: ❌ FAIL
      └─> [Dadu] retorna ao Redator com feedback
          └─> Ciclo de reescrita (Redator → Revisor)

FASE 5: PÓS-PRODUÇÃO
────────────────────
[Dadu]
  ├─ Ajustes finais manuais (opcional)
  ├─ Formatação TJSC (cabeçalho, rodapé)
  ├─ Upload no PJE/eproc
  └─ [Opcional] Extração de conhecimento
      └─ Skill: extracting-dante-knowledge
```

### 4.2 Tempos Estimados por Fase

| Fase | Tempo (caso simples) | Tempo (caso complexo) |
|------|---------------------|----------------------|
| Preparação (Dadu) | 15-20 min | 30-45 min |
| Análise (Analista) | 20-30 min | 45-60 min |
| Diálogo Estratégico | 15-20 min | 30-45 min |
| Blueprint + Handoff | 5-10 min | 10-15 min |
| Redação (Redator) | 10-15 min | 20-30 min |
| Revisão (Revisor) | 5-10 min | 10-15 min |
| **Total Pipeline** | **70-105 min** | **145-210 min** |

**Comparação com método tradicional:**
- Manual: 6-12 horas (360-720 min)
- Sistema Dante: 70-210 min
- **Redução:** ~70-80%

### 4.3 Pontos de Decisão Críticos

**D1: Após Análise Preliminar (Analista)**
- Continuar diálogo estratégico?
- Solicitar jurisprudência adicional?
- Necessário aprofundar provas?

**D2: Após Blueprint (Analista)**
- Blueprint completo e autossuficiente?
- Necessário ajustar ratio decidendi?
- Prosseguir para Handoff?

**D3: Após Voto v1 (Redator)**
- Satisfeito com qualidade?
- Necessário ajuste iterativo?
- Enviar para Revisor?

**D4: Após Validation Report (Revisor)**
- Score ≥ 80? → Aprovar
- Score < 80? → Reescrever
- Falhas críticas? → Bloquear

---

## 5. PERFIL DO OPERADOR (DADU)

### 5.1 Características Profissionais

**Nome:** Dadu  
**Perfil:** Operador humano especialista em Direito Criminal  
**Função:** Orchestrator e Quality Control do Sistema Dante  
**Experiência:** Desembargador / Assessor jurídico sênior (inferido)

**Competências:**
- Domínio de Direito Penal brasileiro
- Conhecimento de jurisprudência (STF, STJ, TJSC)
- Experiência em votos de apelação criminal
- Familiaridade com PJE/eproc (sistemas processuais)
- Expertise em engenharia de prompts para LLMs

### 5.2 Como Dadu Usa o Sistema Dante

**Estilo de Trabalho:**
- **Iterativo:** Prefere ciclos de refinamento a outputs únicos
- **Colaborativo:** Engaja em diálogo estratégico com Analista
- **Exigente:** Busca alta qualidade e conformidade rigorosa
- **Pragmático:** Valoriza economia de tempo sem comprometer qualidade

**Workflow Típico:**

1. **Manhã:** Priorização de casos (ordem de complexidade)
2. **Intake:** 30-45 min por caso (preparação documentos + kickoff)
3. **Pipeline:** 1-2 casos simultâneos em fases diferentes
   - Analista trabalhando em Caso A
   - Redator trabalhando em Caso B
   - Revisor validando Caso C
4. **Tarde:** Ajustes finais e entrega
5. **Meta diária:** 3-5 votos completos

**Comandos Frequentes:**
- "Gerar blueprint"
- "Gerar handoff"
- "Adicione jurisprudência X"
- "Reescreva Tese Y com ênfase em Z"
- "Modo revisão adversarial"

### 5.3 Necessidades e Expectativas

**Do Sistema Dante:**
- ✅ Velocidade: Redução de 70-80% do tempo
- ✅ Qualidade: Manutenção de padrões judiciais
- ✅ Conformidade: Automática e garantida
- ✅ Rastreabilidade: Total e auditável
- ✅ Flexibilidade: Ajustes iterativos fáceis

**Dores que o Sistema Resolve:**
- ❌ Tempo excessivo de redação manual
- ❌ Risco de erros factuais
- ❌ Citações incorretas de jurisprudência
- ❌ Inconsistências de formatação
- ❌ Falta de rastreabilidade de provas

**Do Centro de Controle (CC):**
- Evolução contínua do sistema
- Diagnóstico rápido de problemas
- Criação de novos prompts/agentes
- Otimização de workflows
- Documentação sempre atualizada

### 5.4 Interação com Claude Code (CC)

**Expectativas:**
- CC como "cérebro" da evolução do Dante
- Capacidade de projetar novos agentes
- Refinamento automático de prompts
- Análise de performance e bottlenecks
- Sugestões proativas de melhorias

**Comandos Esperados para CC:**
- `/lint [prompt/workflow]` — Auditoria de qualidade
- `/design [novo agente/feature]` — Geração de variantes
- `/simulate [artefato]` — Dry-run através dos gates
- `/policy [validação]` — Checagem de conformidade
- `/pack [versionamento]` — Empacotamento multi-modelo

---

## 6. FILOSOFIA E DESIGN PRINCIPLES

### 6.1 Princípios Fundamentais

#### 1. **Constraint-Based Approach**
Políticas rígidas (P1-P8) previnem falhas comuns de LLMs em contextos jurídicos. Melhor restringir ex-ante do que corrigir ex-post.

#### 2. **Separation of Concerns**
Cada agente tem responsabilidade única e bem definida:
- Analista: Estratégia
- Redator: Execução
- Revisor: Qualidade
- Maestro: Governança

#### 3. **Maximum Informativit**
Blueprint e Handoff devem ser autossuficientes. Redator trabalha SEM voltar aos autos originais.

#### 4. **Traceability First**
Toda afirmação factual rastreável. Toda jurisprudência identificável. Toda decisão auditável.

#### 5. **Human-in-the-Loop (Critical)**
IA propõe, humano decide. Pontos de decisão críticos sempre com aprovação do operador.

#### 6. **Fail-Safe Defaults**
Em caso de ambiguidade ou conflito, o sistema bloqueia e pede clarificação. Melhor não fazer do que fazer errado.

#### 7. **Cross-Platform Optimization**
Usar pontos fortes de cada modelo:
- Gemini: Contexto longo (Analista)
- Claude: Raciocínio profundo (Redator)

#### 8. **Iterative Refinement**
Preferência por ciclos de melhoria a outputs únicos "perfeitos".

### 6.2 Anti-Patterns Evitados

❌ **Ementa automática:** Risco de simplificação excessiva  
❌ **Cópia integral:** Plágio e violação de direitos autorais  
❌ **Invenção factual:** "Alucinações" de LLM em contexto jurídico  
❌ **Citação vaga:** "STJ já decidiu..." sem identificação  
❌ **Modo Júri ignorado:** Afirmações categóricas em crimes dolosos  
❌ **Dispositivo alterado:** Mudanças no texto oficial do dispositivo  
❌ **Handoff sem Blueprint:** Pipeline desorganizado  

### 6.3 Lições Aprendidas (v4 → v5)

**Evolução v4.1 → v5.0 → v5.1 → v5.2:**

1. **v4.1 (baseline):** Sistema funcional mas verboso
   - Handoff muito grande (~2700 tokens)
   - Maestro reativo (não proativo)
   - Testes ausentes

2. **v5.0 (economia radical):** Over-optimization
   - Handoff enxuto (-72% tokens)
   - MAS: Perda de contexto essencial
   - Qualidade prejudicada em casos complexos

3. **v5.1 (balanceamento):** Production-ready (91/100)
   - Economia inteligente (campos opcionais)
   - Validation hooks automáticos
   - Testes de regressão documentados
   - Troubleshooting guide completo

4. **v5.2 (refinamento):** Estado atual
   - Campo `<estrutura_esperada>` no Handoff
   - Exemplos enriquecidos
   - Cross-platform integration refinada
   - Casos edge documentados

**Métricas de Melhoria:**
- v5.1 vs. v5.0: +13 pontos (+17%)
- v5.1 vs. v4.1: +21 pontos (+30%)
- Production-readiness: 78 → 91/100

---

## 7. TECNOLOGIA E IMPLEMENTAÇÃO

### 7.1 Stack Tecnológico

**Modelos AI:**
- Google Gemini 2.0 Flash (Analista, Revisor)
- Claude Sonnet 4.5 (Redator)
- Multi-modelo (Maestro — agnóstico)

**Ambientes de Execução:**
- **Google AI Studio:** System Instructions (Gemini)
- **Claude.ai Projects:** Project Knowledge + Memory (Claude)
- **Claude Code:** Centro de Controle (futuro)

**Formatos de Dados:**
- **XML:** Handoff (kickoff_redator v5.2)
- **Markdown:** Blueprint, Voto, documentação
- **JSON:** Metadados, Validation Reports, schemas

**Infraestrutura:**
- Cloud-based (Google AI, Anthropic)
- Sem backend próprio (por ora)
- File-based transfer entre agentes

### 7.2 Schemas e Especificações

**Handoff XML Schema v5.2:**
```xml
<kickoff_redator version="5.2">
  <!-- Metadados Processuais -->
  <processo>
    <numero>CNJ</numero>
    <orgao>TJSC - Câmara</orgao>
    <natureza>apelacao_criminal</natureza>
  </processo>

  <!-- Partes -->
  <partes>
    <recorrente>Nome</recorrente>
    <recorrido>Nome</recorrido>
    <advogado_recorrente>Nome - OAB</advogado_recorrente>
  </partes>

  <!-- Tipo de Peça -->
  <tipo_peca>voto</tipo_peca>

  <!-- Modo Júri (condicional) -->
  <banner_modo_juri enabled="true|false">
    <crime_base>Descrição</crime_base>
    <orientacao>Linguagem de prelibação...</orientacao>
  </banner_modo_juri>

  <!-- Estrutura Esperada (NOVO v5.2) -->
  <estrutura_esperada>
    <tem_preliminares>true|false</tem_preliminares>
    <tem_dosimetria>true|false</tem_dosimetria>
    <numeracao>hierarquica</numeracao>
    <secoes_merito>
      <secao>2.1. [Título]</secao>
      <secao>2.2. [Título]</secao>
    </secoes_merito>
  </estrutura_esperada>

  <!-- Fundamentos -->
  <fundamentos>
    <contexto_processual>[opcional]</contexto_processual>
    
    <provas>
      <prova id="P01">
        <tipo>documental|pericial|oral</tipo>
        <descricao>...</descricao>
        <localizacao>fls. X / evento Y</localizacao>
      </prova>
    </provas>

    <teses>
      <tese id="1">
        <nome>...</nome>
        <ratio_decidendi>...</ratio_decidendi>
        <procedencia>PROCEDENTE|IMPROCEDENTE</procedencia>
        <fundamentacao>...</fundamentacao>
        <jurisprudencias>
          <jur id="JUR01">
            <tribunal>STJ</tribunal>
            <numero>REsp 123456</numero>
            <relator>Min. João</relator>
            <data>2024-03-15</data>
            <ementa>...</ementa>
          </jur>
        </jurisprudencias>
      </tese>
    </teses>

    <peculiaridades>[opcional]</peculiaridades>
    <sensibilidades>[opcional]</sensibilidades>
  </fundamentos>

  <!-- Escopo -->
  <escopo>
    <objetivo>...</objetivo>
    <extensao_palavras>800-1200</extensao_palavras>
    <estilo>formal_tecnico</estilo>
    <foco_redacional>
      <desafios>
        <item>...</item>
      </desafios>
      <requisitos>
        <item>...</item>
      </requisitos>
    </foco_redacional>
  </escopo>

  <!-- Dispositivo Canônico (IMUTÁVEL) -->
  <dispositivo_canonico>
    [Texto EXATO que deve aparecer no voto]
  </dispositivo_canonico>

  <!-- Orientações -->
  <nao_fazer>
    <item>Não produzir ementa</item>
    <item>Não copiar sentença</item>
    <item>Não alterar dispositivo</item>
  </nao_fazer>

  <!-- Anexos (opcional) -->
  <anexos>
    <anexo tipo="transcricao">
      <id>TRANS01</id>
      <descricao>Audiência de instrução</descricao>
      <path>transcricao_audiencia.md</path>
    </anexo>
  </anexos>
</kickoff_redator>
```

**Blueprint Structure (Markdown):**
```markdown
# BLUEPRINT — [Nome do Caso]

## METADADOS
- Processo: [Número CNJ]
- Tipo: Apelação Criminal
- Crime: [Art. XX CP]
- Modo Júri: [SIM/NÃO]

## I. SÍNTESE DO CASO
[Narrativa fática: 200-300 palavras]

## II. PROVAS CATALOGADAS

### Provas Documentais
- **P01**: [Descrição] (fls. X-Y / evento Z)

### Provas Periciais
- **P02**: [Descrição] (fls. X-Y / evento Z)

### Provas Orais
- **P03**: [Testemunha] (fls. X-Y / evento Z)
  - Pontos-chave: [...]
  - Contradições: [...]

## III. TESES MAPEADAS

### Tese 1: [Nome da Tese]
**Fundamento Legal:** [Arts.]
**Argumentos do Recorrente:**
- [...]

**Contra-Argumentos:**
- [...]

**Provas Relevantes:** P01, P02

**Análise Preliminar:** [PROCEDENTE/IMPROCEDENTE]

**Ratio Decidendi:**
[Núcleo do raciocínio jurídico - 2-3 parágrafos]

**Jurisprudências Aplicáveis:**
- **JUR01**: STJ, REsp 123456, Rel. Min. João, j. 15/03/2024
  > [Ementa relevante]

### Tese 2: [...]
[Repetir estrutura]

## IV. DOSIMETRIA (se aplicável)

### Primeira Fase
[Análise das circunstâncias judiciais]

### Segunda Fase
[Agravantes e Atenuantes]

### Terceira Fase
[Causas de aumento/diminuição]

### Regime Inicial
[Fundamentação]

## V. DISPOSITIVO CANÔNICO

```
[Texto EXATO do dispositivo]
```

## VI. ORIENTAÇÕES REDACIONAIS

**Foco Principal:** [...]
**Ênfase em:** [...]
**Cuidados:** [...]
**Extensão Estimada:** 800-1200 palavras

---

**Status:** Aprovado para Handoff  
**Data:** [DD/MM/YYYY]
```

### 7.3 Métricas de Performance

**Token Economy (Handoff):**
| Tipo de Caso | Tokens v4.1 | Tokens v5.2 | Economia |
|--------------|-------------|-------------|----------|
| Simples      | 2700        | 600         | -78%     |
| Médio        | 2700        | 1100        | -60%     |
| Complexo     | 2700        | 1800        | -35%     |

**Qualidade (Revisor Scores):**
| Dimensão        | Média v4.1 | Média v5.2 | Melhoria |
|-----------------|------------|------------|----------|
| Fidelidade      | 82/100     | 91/100     | +11%     |
| Conformidade    | 79/100     | 94/100     | +19%     |
| Jurisprudência  | 75/100     | 88/100     | +17%     |
| Estrutura       | 88/100     | 96/100     | +9%      |
| Estilo          | 81/100     | 89/100     | +10%     |
| **Global**      | **81/100** | **92/100** | **+14%** |

**Tempo de Produção:**
| Fase                | v4.1 (min) | v5.2 (min) | Melhoria |
|---------------------|------------|------------|----------|
| Análise             | 45-60      | 35-50      | -22%     |
| Diálogo             | 30-45      | 20-35      | -33%     |
| Blueprint/Handoff   | 15-20      | 8-12       | -40%     |
| Redação             | 25-40      | 15-25      | -40%     |
| Revisão             | 15-20      | 10-15      | -33%     |
| **Total Pipeline**  | **130-185**| **88-137** | **-32%** |

---

## 8. EVOLUÇÃO E ROADMAP

### 8.1 Histórico de Versões

**v4.1 (baseline - Q2 2024):**
- Sistema funcional, mas verboso
- Handoff grande (~2700 tokens)
- Maestro reativo
- Sem testes formais
- Score: 70/100

**v5.0 (economia radical - Q3 2024):**
- Over-optimization de tokens (-72%)
- Perda de contexto essencial
- Qualidade prejudicada em casos complexos
- Score: 78/100 (melhoria, mas instável)

**v5.1 (production-ready - Q4 2024):**
- Balanceamento tokens vs. contexto
- Validation hooks automáticos
- Testes de regressão documentados
- Troubleshooting guide
- Score: 91/100 ✅

**v5.2 (refinamento - Q4 2024 / atual):**
- Campo `<estrutura_esperada>` no Handoff
- Exemplos enriquecidos por política
- Casos edge documentados
- Cross-platform integration otimizada
- Score: 92/100 ✅

### 8.2 Roadmap Futuro

**v5.3 (Q1 2025):**
- Componente Estilista (refinamento de português)
- Skills do Claude Code integrados
- Adaptive complexity (ajuste dinâmico)
- Melhorias incrementais no Revisor

**v6.0 (Q2-Q3 2025):**
- Multi-modal integration (imagens, áudios)
- Parallel processing (múltiplos casos simultâneos)
- Dashboard de observabilidade
- Templates por tipo de peça
- Neural architecture search

**v7.0+ (2026+):**
- Expansão para outras áreas do Direito
- Outros tribunais (TRFs, TRTs)
- Integração nativa com PJE/eproc
- Auto-aprendizado supervisionado

### 8.3 Desafios e Oportunidades

**Desafios Técnicos:**
- Manter qualidade ao escalar para volumes maiores
- Garantir consistência cross-platform (Gemini/Claude)
- Evitar "model drift" com atualizações de LLMs
- Integração com sistemas legados (PJE)

**Oportunidades:**
- Expandir para outras câmaras do TJSC
- Criar knowledge base de jurisprudências
- Desenvolver sistema de recomendação de precedentes
- Automatizar extração de conhecimento de votos finalizados

---

## 9. PLANO DE INFRAESTRUTURA CC (CENTRO DE CONTROLE)

Este é o coração da documentação para o Claude Code. Aqui está o plano completo para construir o Centro de Controle do Sistema Dante.

### 9.1 Visão Geral do CC

O **Centro de Controle (CC)** é um agente meta-sistema que opera sobre o Sistema Dante, responsável por:

1. **Evolução contínua** do sistema via engenharia de prompt
2. **Design e validação** de workflows ponta a ponta
3. **Criação e refinamento** de prompts multi-modelo
4. **Orquestração** do pipeline Analista → Handoff → Redator → Revisor
5. **Auditoria de qualidade** via checklists e simulações
6. **Diagnóstico** de problemas e troubleshooting
7. **Documentação** viva e sempre atualizada

**Analogia:** Se o Sistema Dante é o "corpo", o CC é o "cérebro" que coordena, evolui e otimiza.

---

[Continua na Parte 2...]
