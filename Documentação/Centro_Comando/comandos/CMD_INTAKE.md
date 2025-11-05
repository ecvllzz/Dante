# COMANDO: /intake
## Captura e Saneamento de Escopo

**Versão:** 1.0.0
**Frequência de Uso:** 15%
**Criticidade:** Alta

---

## 🎯 OBJETIVO

Capturar objetivo, restrições, critérios de sucesso e artefatos esperados de uma nova tarefa. O `/intake` é o primeiro passo em qualquer evolução do Sistema Dante.

---

## 📥 REQUEST SCHEMA

```json
{
  "objetivo": "string (claro e mensurável)",
  "restricoes": ["string", "string"],
  "sucesso_esperado": "string (critérios mensuráveis)",
  "artefatos_alvo": ["PromptPack|Workflow|Handoff|Relatorio"],
  "modelos_alvo": ["Claude|Gemini|ChatGPT"],
  "contexto_fornecido": "string|null (opcional)"
}
```

### Campos Obrigatórios

- `objetivo`: Descrição clara do que se quer alcançar
- `sucesso_esperado`: Como saber que foi bem-sucedido

### Campos Opcionais

- `restricoes`: Limitações técnicas, de tempo, de escopo
- `artefatos_alvo`: Que outputs são esperados
- `modelos_alvo`: Quais LLMs serão usados
- `contexto_fornecido`: Informação adicional relevante

---

## 📤 RESPONSE SCHEMA

```json
{
  "intake_report": {
    "objetivo": "string (normalizado)",
    "restricoes_normalizadas": ["string"],
    "sucesso_criterios": ["string (mensuráveis)"],
    "artefatos_confirmados": ["string"],
    "lacunas": ["string (perguntas para operador)"],
    "risco_inicial": ["string (riscos identificados)"]
  },
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 123
  }
}
```

---

## ✅ DEFINITION OF DONE (DoD)

O `/intake` está completo quando:

- [x] Objetivo normalizado e claro
- [x] Restrições identificadas e categorizadas
- [x] Critérios de sucesso são mensuráveis
- [x] Artefatos alvo confirmados
- [x] Lacunas de informação mapeadas
- [x] Riscos iniciais avaliados
- [x] Response no schema definido

---

## 🔍 PROCESSO DE INTAKE

### 1. Normalização do Objetivo

Transformar objetivo vago em objetivo SMART:

- **S**pecific (Específico)
- **M**easurable (Mensurável)
- **A**chievable (Alcançável)
- **R**elevant (Relevante)
- **T**ime-bound (Com prazo, se aplicável)

**Exemplo:**

❌ Vago: "Melhorar o Revisor"
✅ Normalizado: "Reduzir falsos positivos do Revisor em P3 (Modo Júri) em ≥50%"

### 2. Categorização de Restrições

Classificar restrições em:

- **Técnicas**: Modelo, ambiente, tokens
- **Temporais**: Prazos, urgência
- **Escopo**: O que está fora do escopo
- **Qualidade**: Padrões mínimos esperados

### 3. Definição de Critérios de Sucesso

Criar critérios mensuráveis:

- **Quantitativos**: Scores, percentuais, tempos
- **Qualitativos**: Checklist de características
- **Comparativos**: vs. versão anterior

### 4. Mapeamento de Lacunas

Identificar o que falta para começar:

- Informações ausentes
- Decisões pendentes
- Ambiguidades
- Fontes de dados necessárias

### 5. Avaliação de Riscos

Classificar riscos:

- **BAIXO**: Baixa probabilidade, baixo impacto
- **MÉDIO**: Média probabilidade ou médio impacto
- **ALTO**: Alta probabilidade ou alto impacto

---

## 📋 EXEMPLOS DE USO

### Exemplo 1: Criar Novo Agente

**Request:**
```
/intake
Objetivo: Criar novo agente "Estilista" para refinamento de linguagem
Restrições: Deve operar no Claude, integrar entre Redator e Revisor
Sucesso: Voto com linguagem mais natural, score estilo +10%
Artefatos: PromptPack + Workflow
Modelos: Claude
```

**Response:**
```json
{
  "intake_report": {
    "objetivo": "Criar agente Estilista (Claude) para refinamento linguístico pós-Redator, visando aumento de +10% no score de estilo",
    "restricoes_normalizadas": [
      "Modelo: Claude Sonnet 4.5",
      "Posição no pipeline: Entre Redator e Revisor",
      "Escopo: Apenas linguagem (não estrutura ou conteúdo)",
      "Compatibilidade: Deve preservar todas as políticas P1-P8"
    ],
    "sucesso_criterios": [
      "Score de estilo aumenta ≥10% vs. baseline",
      "Variação de conectivos aumenta ≥15 tipos diferentes",
      "Parágrafos bem dimensionados (3-6 linhas) em 80%+ do texto",
      "Modo Júri preservado quando aplicável",
      "Sem alteração de fatos, jurisprudências ou dispositivo"
    ],
    "artefatos_confirmados": [
      "D_Estilista_v0.1.md (prompt Claude)",
      "Workflow_Estilista.md (integração no pipeline)",
      "Test_Suite_Estilista.md (casos de teste)"
    ],
    "lacunas": [
      "Como operador acionará Estilista? Comando manual ou automático?",
      "Estilista deve sempre rodar ou só quando score estilo <80?",
      "Output do Estilista: sobrescrever voto original ou criar versão 'v2_styled'?",
      "Tempo máximo aceitável: quanto?"
    ],
    "risco_inicial": [
      "MÉDIO: Integração entre Redator e Revisor adiciona complexidade ao pipeline",
      "BAIXO: Claude já demonstrou boa capacidade de refinamento linguístico",
      "MÉDIO: Risco de 'over-styling' (perder tom técnico-jurídico necessário)",
      "BAIXO: Impacto em políticas P1-P8 se bem projetado"
    ]
  },
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 234
  }
}
```

### Exemplo 2: Refinar Agente Existente

**Request:**
```
/intake
Objetivo: Corrigir falsos positivos do Revisor em P3 (Modo Júri)
Sucesso: Redução de ≥50% em falsos positivos
Artefatos: D_Revisor_v5.4 (hotfix)
Contexto: Palavras como 'hábil', 'ardiloso' sendo flagged incorretamente
```

**Response:**
```json
{
  "intake_report": {
    "objetivo": "Reduzir falsos positivos do Revisor v5.3 em validação P3 (Modo Júri) em ≥50%, preservando verdadeiros positivos",
    "restricoes_normalizadas": [
      "Modelo: Gemini 2.0 Flash (Revisor)",
      "Escopo: Apenas correção P3, sem alterar outras validações",
      "Compatibilidade: Manter ou melhorar score geral do Revisor",
      "Versão: Hotfix → v5.4 (PATCH, não MINOR)"
    ],
    "sucesso_criterios": [
      "Falsos positivos P3 reduzem ≥50% vs. v5.3",
      "Verdadeiros positivos P3 mantidos ≥95%",
      "Score global do Revisor não degrada (≥92/100)",
      "Teste de regressão passa em 10 casos conhecidos"
    ],
    "artefatos_confirmados": [
      "D_Revisor_v5.4.md (prompt corrigido)",
      "CHANGELOG_v5.4.md (documentação da correção)",
      "Test_Suite_P3_Regression.md (casos de teste)"
    ],
    "lacunas": [
      "Exemplos completos de falsos positivos? (contexto completo)",
      "Há whitelist de palavras OK em Modo Júri?",
      "Operador tem casos de teste prontos para validação?"
    ],
    "risco_inicial": [
      "BAIXO: Correção localizada, impacto limitado",
      "MÉDIO: Risco de introduzir novos falsos negativos ao corrigir falsos positivos",
      "BAIXO: Hotfix rápido, rollback fácil se necessário"
    ]
  },
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 189
  }
}
```

---

## ⚠️ ALERTAS E VALIDAÇÕES

### Bloquear se:

- ❌ Objetivo é vago e não mensurável
- ❌ Fontes não autorizadas são mencionadas
- ❌ Sucesso esperado não é verificável
- ❌ Conflito com políticas P1-P8 identificado

### Avisar se:

- ⚠️ Restrições são muito amplas ou vagas
- ⚠️ Artefatos alvo não são claros
- ⚠️ Riscos altos identificados
- ⚠️ Lacunas críticas de informação

---

## 🔗 PRÓXIMOS PASSOS APÓS INTAKE

Após `/intake` completo:

1. **Operador responde lacunas** → Clarifica informações faltantes
2. **CC executa `/design`** → Gera variantes de solução
3. **Operador escolhe variante** → Decide abordagem
4. **CC cria artefatos** → Implementa solução
5. **CC valida** → `/lint` + `/simulate` + `/policy`
6. **CC empacota** → `/pack` com versionamento

---

## 📊 MÉTRICAS DE QUALIDADE

Um bom `/intake` deve ter:

- ✅ Objetivo claro em 1-2 frases
- ✅ ≥2 critérios de sucesso mensuráveis
- ✅ Lacunas identificadas (mesmo se = 0)
- ✅ Riscos avaliados e categorizados
- ✅ Response em ≤60 segundos

---

**Última Atualização:** 2025-11-05
**Próxima Revisão:** Após 10 usos reais
