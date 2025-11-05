# COMANDO: /design
## Geração de Variantes de Solução

**Versão:** 1.0.0
**Frequência de Uso:** 30%
**Criticidade:** Alta

---

## 🎯 OBJETIVO

Gerar 2-3 variantes de solução (prompts, workflows, agentes) com análise de trade-offs, vantagens, riscos e recomendação justificada.

---

## 📥 REQUEST SCHEMA

```json
{
  "insumos": "string (descrição do problema/objetivo)",
  "criterios_decisao": ["string", "string"],
  "alvos": "Prompt|Workflow|Agente|Handoff"
}
```

### Campos Obrigatórios

- `insumos`: Contexto completo do que precisa ser desenhado
- `alvos`: Tipo de artefato a ser criado

### Campos Opcionais

- `criterios_decisao`: Critérios para comparar variantes (ex: velocidade, qualidade, simplicidade)

---

## 📤 RESPONSE SCHEMA

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
      "votos": {
        "v1": "A|B|C",
        "v2": "A|B|C",
        "v3": "A|B|C"
      }
    }
  ],
  "recomendacao": "string (variante recomendada)",
  "rationale": "string (justificativa da recomendação)",
  "tests": ["string (testes sugeridos)"],
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 456
  }
}
```

---

## ✅ DEFINITION OF DONE (DoD)

O `/design` está completo quando:

- [x] ≥2 variantes propostas
- [x] Cada variante tem ID único (v1, v2, v3)
- [x] Vantagens e riscos claros para cada
- [x] Matriz de trade-offs comparável
- [x] Recomendação explícita e justificada
- [x] Testes sugeridos para cada variante
- [x] Response no schema definido

---

## 🎨 PROCESSO DE DESIGN

### 1. Análise do Problema

- **Entender requisitos**: O que o operador precisa?
- **Identificar restrições**: O que limita as opções?
- **Mapear contexto**: Onde se encaixa no Sistema Dante?

### 2. Geração de Variantes

Gerar 2-3 abordagens diferentes:

- **Variante Conservadora**: Menor risco, mais segura
- **Variante Inovadora**: Mais ambiciosa, maior potencial
- **Variante Híbrida**: Balanceada entre as anteriores

### 3. Análise de Trade-offs

Para cada variante, avaliar:

- **Vantagens**: O que ela faz melhor?
- **Riscos**: O que pode dar errado?
- **Quando usar**: Em que cenário é ideal?

### 4. Matriz de Decisão

Comparar variantes usando critérios:

- **A** = Excelente
- **B** = Bom
- **C** = Aceitável
- **D** = Inadequado

### 5. Recomendação

Escolher variante baseada em:

- Critérios de decisão do operador
- Perfil de risco aceitável
- Contexto atual do projeto
- Facilidade de implementação vs. ganho esperado

---

## 📋 EXEMPLOS DE USO

### Exemplo 1: Criar Agente Estilista

**Request:**
```
/design
Insumos: Criar agente Estilista para refinamento de linguagem pós-Redator
Critérios: Velocidade, Qualidade linguística, Simplicidade de integração
Alvos: Agente
```

**Response:**
```json
{
  "variantes": [
    {
      "id": "v1",
      "descricao": "Estilista Standalone (Pós-Redator)",
      "vantagens": [
        "Isolamento total: não interfere com Redator existente",
        "Facilidade de teste: pode ser desativado sem impacto",
        "Flexibilidade: operador decide quando usar",
        "Rollback fácil se não funcionar"
      ],
      "riscos": [
        "Workflow manual adicional para operador",
        "Possível duplicação de trabalho se Redator já fez estilo bem",
        "Mais um passo no pipeline (aumenta tempo total)"
      ],
      "quando_usar": "Quando operador quer controle fino sobre estilização e prefere segurança"
    },
    {
      "id": "v2",
      "descricao": "Estilista Integrado no Redator (Single-Pass)",
      "vantagens": [
        "Zero overhead de workflow: acontece automaticamente",
        "Mais rápido: uma única passada",
        "Menos transferências de dados entre agentes",
        "UX mais simples para operador"
      ],
      "riscos": [
        "Aumento de complexidade do prompt Redator",
        "Difícil de isolar problemas (bug no Redator ou Estilista?)",
        "Rollback complexo se não funcionar",
        "Pode sobrecarregar contexto do Redator"
      ],
      "quando_usar": "Quando simplicidade operacional é prioridade #1 e Redator pode absorver complexidade"
    },
    {
      "id": "v3",
      "descricao": "Estilista Condicional (Trigger por Score)",
      "vantagens": [
        "Eficiência: só roda quando necessário (score estilo <80)",
        "Automação inteligente: operador não precisa decidir",
        "Otimiza tempo em casos que já têm bom estilo",
        "Melhor UX a longo prazo"
      ],
      "riscos": [
        "Complexidade na orquestração (Revisor → Estilista → Revisor)",
        "Potencial loop infinito se Estilista não melhorar score",
        "Debugging mais difícil (fluxo condicional)",
        "Depende de Revisor funcionar perfeitamente"
      ],
      "quando_usar": "Quando eficiência e automação são equilibradas, após v1 validado"
    }
  ],
  "matriz_tradeoffs": [
    {
      "criterio": "Velocidade",
      "votos": {
        "v1": "B (adiciona passo manual ~30s)",
        "v2": "A (single-pass, zero overhead)",
        "v3": "A (pula quando não necessário)"
      }
    },
    {
      "criterio": "Qualidade linguística",
      "votos": {
        "v1": "A (foco dedicado, sem competição)",
        "v2": "B (competição por atenção com outras tarefas)",
        "v3": "A (foco dedicado quando necessário)"
      }
    },
    {
      "criterio": "Simplicidade de integração",
      "votos": {
        "v1": "A (isolado, zero impacto em código existente)",
        "v2": "C (requer reescrever Redator)",
        "v3": "B (requer orquestração Revisor + lógica condicional)"
      }
    },
    {
      "criterio": "Flexibilidade operacional",
      "votos": {
        "v1": "A (operador decide tudo manualmente)",
        "v2": "C (automático sempre, sem controle)",
        "v3": "B (automático quando threshold, semi-controle)"
      }
    },
    {
      "criterio": "Facilidade de debugging",
      "votos": {
        "v1": "A (isolado, fácil de testar e isolar problemas)",
        "v2": "C (difícil separar Redator de Estilista)",
        "v3": "B (fluxo condicional adiciona complexidade)"
      }
    }
  ],
  "recomendacao": "Variante v1 (Standalone) para MVP",
  "rationale": "Para primeira versão (v0.1), variante v1 oferece melhor balance entre segurança (isolamento completo) e aprendizado (podemos testar sem impactar pipeline existente). Após validação em produção com 20-30 casos reais, podemos evoluir para v3 (Condicional) que oferece melhor UX e eficiência a longo prazo. Variante v2 é arriscada demais para primeira implementação dado o impacto no Redator v5.2 que já está estável.",
  "tests": [
    {
      "nome": "Caso simples: voto já bem escrito",
      "objetivo": "Verificar se Estilista melhora marginalmente sem degradar",
      "sucesso": "Score estilo +5% a +10%, sem alteração de fatos/jurisprudências"
    },
    {
      "nome": "Caso médio: voto OK mas pode melhorar",
      "objetivo": "Verificar se Estilista aumenta score significativamente",
      "sucesso": "Score estilo +10% a +15%, linguagem mais natural"
    },
    {
      "nome": "Caso complexo: voto com linguagem técnica demais",
      "objetivo": "Verificar se Estilista humaniza sem perder precisão",
      "sucesso": "Score estilo +15%+, mantém tom técnico-jurídico necessário"
    },
    {
      "nome": "Modo Júri: linguagem de prelibação",
      "objetivo": "Verificar se Estilista preserva P3",
      "sucesso": "Linguagem cautelosa mantida, sem introduzir afirmações categóricas"
    }
  ],
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 567
  }
}
```

---

### Exemplo 2: Refinar Revisor (Falsos Positivos)

**Request:**
```
/design
Insumos: Corrigir falsos positivos do Revisor em P3 (Modo Júri). Palavras como 'hábil', 'ardiloso' sendo flagged incorretamente
Critérios: Eficácia, Manutenibilidade, Impacto no código
Alvos: Prompt
```

**Response:**
```json
{
  "variantes": [
    {
      "id": "v1",
      "descricao": "Whitelist de Palavras Contextuais",
      "vantagens": [
        "Solução simples e direta",
        "Fácil de manter e expandir",
        "Baixo risco de regressão",
        "Operador pode adicionar palavras facilmente"
      ],
      "riscos": [
        "Lista pode crescer muito ao longo do tempo",
        "Pode não capturar todas as nuances contextuais",
        "Manutenção manual contínua necessária"
      ],
      "quando_usar": "Quando há lista finita de palavras problemáticas e contexto é previsível"
    },
    {
      "id": "v2",
      "descricao": "Ajustar Regex com Contexto Semântico",
      "vantagens": [
        "Mais inteligente que whitelist simples",
        "Captura contexto ao redor da palavra",
        "Menos manutenção após calibração inicial",
        "Generaliza melhor para novos casos"
      ],
      "riscos": [
        "Regex complexo é difícil de debugar",
        "Pode introduzir novos falsos negativos",
        "Requer testes extensivos de regressão",
        "Mais difícil para operador ajustar no futuro"
      ],
      "quando_usar": "Quando padrões são reconhecíveis mas precisam de contexto para decisão"
    },
    {
      "id": "v3",
      "descricao": "Análise Semântica via Thinking Block",
      "vantagens": [
        "Mais preciso: entende intenção real",
        "Flexível: adapta-se a contextos novos",
        "Reduz falsos positivos E negativos",
        "Aproveita capacidade de raciocínio do LLM"
      ],
      "riscos": [
        "Aumenta tokens e tempo de processamento",
        "Menos determinístico (variação entre runs)",
        "Difícil de auditar decisão do modelo",
        "Depende de temperatura baixa para consistência"
      ],
      "quando_usar": "Quando precisão é mais importante que velocidade e determinismo"
    }
  ],
  "matriz_tradeoffs": [
    {
      "criterio": "Eficácia (redução FP)",
      "votos": {
        "v1": "B (bom para casos conhecidos)",
        "v2": "A (excelente com contexto)",
        "v3": "A (melhor precisão geral)"
      }
    },
    {
      "criterio": "Manutenibilidade",
      "votos": {
        "v1": "A (lista simples, fácil editar)",
        "v2": "C (regex complexo, difícil manter)",
        "v3": "B (prompt mais longo, mas legível)"
      }
    },
    {
      "criterio": "Impacto no código",
      "votos": {
        "v1": "A (mudança mínima, localizada)",
        "v2": "B (muda lógica de validação)",
        "v3": "B (adiciona thinking block, +tokens)"
      }
    },
    {
      "criterio": "Velocidade",
      "votos": {
        "v1": "A (instant, sem overhead)",
        "v2": "A (fast, só regex)",
        "v3": "C (mais lento, +tokens thinking)"
      }
    },
    {
      "criterio": "Escalabilidade",
      "votos": {
        "v1": "C (lista cresce infinitamente)",
        "v2": "A (regex generaliza)",
        "v3": "A (LLM adapta-se)"
      }
    }
  ],
  "recomendacao": "Variante v2 (Regex com Contexto) para hotfix v5.4",
  "rationale": "v2 oferece melhor balance para hotfix: resolve problema atual (falsos positivos específicos), não adiciona overhead significativo, e generaliza razoavelmente bem. v1 seria muito simplista e não escalável. v3 seria ideal a longo prazo mas requer mais validação e impacta performance. Sugestão: implementar v2 agora para v5.4, e considerar v3 para v6.0 quando houver tempo de testar extensivamente.",
  "tests": [
    {
      "nome": "Regressão: Casos que estavam PASS",
      "objetivo": "Garantir que correção não introduziu novos falsos positivos",
      "sucesso": "10 casos conhecidos que passavam continuam passando"
    },
    {
      "nome": "Correção: Casos que eram falsos positivos",
      "objetivo": "Verificar que palavras como 'hábil', 'ardiloso' não são mais flagged incorretamente",
      "sucesso": "5 casos problemáticos agora passam sem warnings"
    },
    {
      "nome": "Verdadeiros positivos: Modo Júri real",
      "objetivo": "Garantir que violações reais ainda são detectadas",
      "sucesso": "Caso com 'réu matou vítima' continua sendo flagged"
    }
  ],
  "metrics": {
    "schema_compliance": true,
    "elapsed_ms": 489
  }
}
```

---

## 🎯 PRINCÍPIOS DE BOM DESIGN

### 1. Diversidade de Abordagens

Variantes devem ser **genuinamente diferentes**, não apenas variações superficiais.

❌ Ruim: v1=Estilista com 10 instruções, v2=Estilista com 12 instruções
✅ Bom: v1=Standalone, v2=Integrado, v3=Condicional

### 2. Trade-offs Claros

Cada variante deve ter **vantagens E riscos** bem definidos.

Não existe variante "perfeita" em todos os critérios.

### 3. Matriz Comparável

Critérios devem ser:
- **Mensuráveis** ou **avaliáveis objetivamente**
- **Relevantes** para a decisão
- **Independentes** entre si

### 4. Recomendação Justificada

Rationale deve explicar:
- **Por que** esta variante?
- **Por que não** as outras?
- **Quando** reconsiderar?

### 5. Testes Acionáveis

Cada teste deve ter:
- Nome claro
- Objetivo específico
- Critério de sucesso verificável

---

## 📊 MÉTRICAS DE QUALIDADE

Um bom `/design` deve ter:

- ✅ ≥2 variantes genuinamente diferentes
- ✅ ≥3 vantagens por variante
- ✅ ≥2 riscos por variante
- ✅ Matriz com ≥3 critérios relevantes
- ✅ Recomendação com ≥3 frases de justificativa
- ✅ ≥3 testes sugeridos
- ✅ Response em ≤120 segundos

---

## 🔗 PRÓXIMOS PASSOS APÓS DESIGN

Após `/design` completo:

1. **Operador escolhe variante** → Decisão explícita
2. **CC cria artefatos** → Implementa variante escolhida
3. **CC valida** → `/lint` + `/simulate` + `/policy`
4. **CC testa** → Executa testes sugeridos
5. **CC empacota** → `/pack` com changelog
6. **Feedback** → Coleta resultados reais

---

**Última Atualização:** 2025-11-05
**Próxima Revisão:** Após 20 designs reais
