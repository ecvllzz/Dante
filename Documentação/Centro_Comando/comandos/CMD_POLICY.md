# COMANDO: /policy
## Validação de Conformidade com Políticas

**Versão:** 1.0.0
**Frequência de Uso:** 40% (comando mais usado)
**Criticidade:** Crítica

---

## 🎯 OBJETIVO

Validar conformidade de prompts, workflows ou handoffs com as 8 políticas fundamentais do Sistema Dante (P1-P8). Emitir alertas de governança quando violações são detectadas.

---

## 📥 REQUEST SCHEMA

```json
{
  "acao": "Validar|Auditar",
  "alvo": "Prompt|Workflow|Handoff|Voto",
  "conteudo": "string (conteúdo completo a validar)"
}
```

### Campos Obrigatórios

- `acao`: Tipo de validação
  - **Validar**: Checagem rápida pass/fail
  - **Auditar**: Análise profunda com detalhes
- `alvo`: Tipo de artefato sendo validado
- `conteudo`: Texto completo do artefato

---

## 📤 RESPONSE SCHEMA

```json
{
  "policy_checklist_result": {
    "passes": ["P1", "P3", "P4", "P5", "P6", "P7", "P8"],
    "fails": ["P2"]
  },
  "alertas": [
    {
      "xml": "<alerta_governanca version=\"1.0\">...</alerta_governanca>"
    }
  ],
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 234
  }
}
```

---

## 📋 POLÍTICAS VALIDADAS (P1-P8)

### P1: FIDELIDADE AOS AUTOS
**Severidade:** CRITICAL

**O que valida:**
- Toda afirmação factual tem rastreabilidade
- IDs de prova (P01, P02) ou referências (fls. X, evento Y)
- Sem inferências apresentadas como fatos

**Violações comuns:**
- "A testemunha disse X" sem citar fonte
- "Ficou comprovado Y" sem prova específica
- Uso de "certamente", "obviamente" sem base

---

### P2: VEDAÇÃO DE EMENTA
**Severidade:** CRITICAL

**O que valida:**
- Ausência de seção "EMENTA"
- Ausência de resumo antes do Relatório
- Voto inicia diretamente com "I. RELATÓRIO"

**Violações comuns:**
- Qualquer seção rotulada "EMENTA"
- Texto resumido antes do Relatório
- Parágrafo inicial com resumo decisório

---

### P3: MODO JÚRI
**Severidade:** HIGH

**O que valida:**
- Linguagem de prelibação em crimes dolosos contra a vida
- Uso de "indícios", "elementos indicam", "aparenta"
- Evita afirmações categóricas sobre autoria

**Violações comuns:**
- "Réu matou vítima" em vez de "há indícios"
- "Autor do crime" sem cautela linguística
- Ignorar banner `<banner_modo_juri enabled="true"/>`

---

### P4: RASTREABILIDADE DE JURISPRUDÊNCIA
**Severidade:** HIGH

**O que valida:**
- Toda jurisprudência tem Tribunal + Número mínimo
- Ideal: Tribunal + Número + Relator + Data
- Sem citações vagas ("STJ já decidiu...")

**Violações comuns:**
- "Conforme jurisprudência consolidada..."
- "O tribunal já decidiu..." sem identificação
- Citação sem número do processo

---

### P5: VEDAÇÃO DE CÓPIA INTEGRAL
**Severidade:** CRITICAL

**O que valida:**
- Sem cópia de parágrafos inteiros
- Citações curtas (≤3 linhas) entre aspas OK
- Paráfrases substanciais usadas

**Violações comuns:**
- Copiar fundamentação de sentença
- Reproduzir petição sem paráfrase
- Blocos >3 linhas sem aspas

---

### P6: FIDELIDADE À BLUEPRINT
**Severidade:** HIGH

**O que valida:**
- Linha argumentativa da Blueprint seguida
- Desvios significativos justificados
- Consulta ao operador quando necessário

**Violações comuns:**
- Ignorar estratégia da Blueprint
- Adicionar argumentos não previstos
- Omitir teses da Blueprint

---

### P7: DISPOSITIVO CANÔNICO
**Severidade:** CRITICAL

**O que valida:**
- Dispositivo EXATAMENTE igual ao Handoff
- Zero alterações (nem vírgulas)
- Texto imutável preservado

**Violações comuns:**
- Parafrasear dispositivo
- Adicionar/remover palavras
- Alterar ordem dos termos

---

### P8: BLUEPRINT ANTES DE HANDOFF
**Severidade:** HIGH

**O que valida:**
- Handoff só gerado após Blueprint completo
- Fase de diálogo estratégico executada
- Blueprint validado antes de prosseguir

**Violações comuns:**
- Gerar Handoff sem Blueprint
- Pular diálogo estratégico
- Handoff antes de validação

---

## ✅ DEFINITION OF DONE (DoD)

O `/policy` está completo quando:

- [x] Todas as 8 políticas foram checadas
- [x] Passes e fails claramente identificados
- [x] Alertas XML emitidos para todas as violações
- [x] Severidade corretamente classificada
- [x] Sugestões de correção fornecidas
- [x] Response no schema definido

---

## 🚨 FORMATO DE ALERTA DE GOVERNANÇA

Quando uma violação é detectada, emitir alerta XML:

```xml
<alerta_governanca version="1.0">
  <timestamp>2025-11-05T10:30:00Z</timestamp>

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
  </impacto>

  <alternativa_compativel>
    [Passo sugerido em conformidade com a política]
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

---

## 📋 EXEMPLOS DE USO

### Exemplo 1: Validação com PASS Total

**Request:**
```
/policy
Ação: Validar
Alvo: Prompt
Conteúdo: [D_Redator_v5.2.md completo]
```

**Response:**
```json
{
  "policy_checklist_result": {
    "passes": ["P1", "P2", "P3", "P4", "P5", "P6", "P7", "P8"],
    "fails": []
  },
  "alertas": [],
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 234
  }
}
```

✅ **Interpretação:** Prompt D_Redator_v5.2 está 100% conforme.

---

### Exemplo 2: Violação P2 Detectada

**Request:**
```
/policy
Ação: Auditar
Alvo: Voto
Conteúdo: [Voto com ementa gerada]
```

**Response:**
```json
{
  "policy_checklist_result": {
    "passes": ["P1", "P3", "P4", "P5", "P6", "P7", "P8"],
    "fails": ["P2"]
  },
  "alertas": [
    {
      "xml": "<alerta_governanca version=\"1.0\">
        <timestamp>2025-11-05T10:30:00Z</timestamp>
        <violacao codigo=\"VedacaoEmenta\"/>
        <fonte_politica>[D] Maestro V5.2 / Política P2</fonte_politica>
        <trecho_conflitante>
          <![CDATA[
          EMENTA
          APELAÇÃO CRIMINAL. FURTO QUALIFICADO. AUTORIA E MATERIALIDADE COMPROVADAS.
          RECURSO CONHECIDO E DESPROVIDO.
          ]]>
        </trecho_conflitante>
        <impacto>
          Produção de ementa proibida levará a rejeição do voto pelo Gabinete.
          Violação de política CRITICAL do Sistema Dante.
        </impacto>
        <alternativa_compativel>
          Remover seção EMENTA completamente e iniciar diretamente com:
          I. RELATÓRIO
          [conteúdo do relatório...]
        </alternativa_compativel>
        <acao_recomendada>Corrigir</acao_recomendada>
        <necessita_confirmacao>true</necessita_confirmacao>
        <severidade>CRITICAL</severidade>
      </alerta_governanca>"
    }
  ],
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 345
  }
}
```

❌ **Interpretação:** Voto violou P2. Bloqueio absoluto, correção obrigatória.

---

### Exemplo 3: Violação P4 (Rastreabilidade)

**Request:**
```
/policy
Ação: Validar
Alvo: Voto
Conteúdo: [Voto com citação vaga de jurisprudência]
```

**Response:**
```json
{
  "policy_checklist_result": {
    "passes": ["P1", "P2", "P3", "P5", "P6", "P7", "P8"],
    "fails": ["P4"]
  },
  "alertas": [
    {
      "xml": "<alerta_governanca version=\"1.0\">
        <timestamp>2025-11-05T10:35:00Z</timestamp>
        <violacao codigo=\"RastreabilidadeInsuficiente\"/>
        <fonte_politica>[D] Maestro V5.2 / Política P4</fonte_politica>
        <trecho_conflitante>
          <![CDATA[
          Conforme jurisprudência consolidada do STJ, o crime de furto qualificado...
          ]]>
        </trecho_conflitante>
        <impacto>
          Citação sem rastreabilidade impede verificação e compromete credibilidade judicial.
        </impacto>
        <alternativa_compativel>
          Adicionar identificação mínima: Tribunal + Número do processo.
          Exemplo: \"Conforme STJ, REsp 1.234.567, o crime de furto qualificado...\"

          Ideal: Incluir também Relator e Data.
          Exemplo: \"Conforme STJ, REsp 1.234.567, Rel. Min. João Silva, j. 15/03/2024...\"
        </alternativa_compativel>
        <acao_recomendada>Corrigir</acao_recomendada>
        <necessita_confirmacao>false</necessita_confirmacao>
        <severidade>HIGH</severidade>
      </alerta_governanca>"
    }
  ],
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 289
  }
}
```

⚠️ **Interpretação:** Voto violou P4. Severidade HIGH, correção recomendada.

---

## 🔍 LÓGICA DE DECISÃO

### Quando BLOQUEAR (CRITICAL)

- P1 violado: Fidelidade comprometida
- P2 violado: Ementa gerada
- P5 violado: Cópia integral detectada
- P7 violado: Dispositivo alterado

**Ação:** Impedir prosseguimento, exigir correção.

### Quando AVISAR (HIGH)

- P3 violado: Modo Júri ignorado
- P4 violado: Jurisprudência sem rastreabilidade
- P6 violado: Blueprint não seguida
- P8 violado: Ordem do pipeline violada

**Ação:** Permitir com alerta, recomendar correção.

### Quando INFORMAR (MEDIUM/LOW)

- Padrões de estilo subótimos
- Oportunidades de melhoria
- Boas práticas não seguidas

**Ação:** Registrar, não bloquear.

---

## ⚙️ CONFIGURAÇÕES AVANÇADAS

### Modo Estrito

```json
{
  "acao": "Auditar",
  "alvo": "Prompt",
  "conteudo": "...",
  "modo": "estrito",
  "bloquear_se_warning": true
}
```

Bloqueia mesmo em violações HIGH, não só CRITICAL.

### Políticas Específicas

```json
{
  "acao": "Validar",
  "alvo": "Voto",
  "conteudo": "...",
  "politicas": ["P2", "P7"]
}
```

Valida apenas P2 e P7, ignora outras.

---

## 📊 MÉTRICAS DE QUALIDADE

Um bom `/policy` deve ter:

- ✅ Checagem completa P1-P8 em ≤60 segundos
- ✅ Alertas XML bem formatados
- ✅ Severidade corretamente classificada
- ✅ Sugestões de correção acionáveis
- ✅ Zero falsos positivos (após calibração)

---

## 🔗 RELACIONAMENTO COM OUTROS COMANDOS

- **Após `/design`**: Validar variantes propostas
- **Após criação de artefato**: Validar conformidade
- **Antes de `/pack`**: Checagem final pré-entrega
- **Em `/simulate`**: Integrado no dry-run

---

**Última Atualização:** 2025-11-05
**Próxima Revisão:** Após 50 validações reais
