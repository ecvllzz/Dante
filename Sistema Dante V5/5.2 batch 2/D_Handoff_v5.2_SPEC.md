# [D] HANDOFF — Especificação Técnica XML v5.2

Este documento especifica a estrutura do Handoff XML que conecta Analista → Redator no Sistema Dante.

## PROPÓSITO

O Handoff XML é o **contrato de interface** entre Analista e Redator. Ele deve conter TODOS os insumos necessários para que o Redator possa redigir o voto SEM precisar voltar aos autos.

---

## VERSÃO

**Versão:** 5.2  
**Changelog v5.2:**

- ✅ Adicionado campo `<estrutura_esperada>` para sinalizar ao Redator estrutura hierárquica
- ✅ Recuperados campos opcionais: `<contexto_processual>`, `<peculiaridades>`, `<sensibilidades>`
- ✅ Adicionado `<provas_orais_detalhadas>` para casos com transcrição
- ✅ Refinada estrutura de `<tese>` com ratio_decidendi explícita
- ✅ Adicionado `<tensoes_probatorias>` por tese

---

## XML SCHEMA

### Root Element

```xml
<kickoff_redator version="5.2">
  <!-- Conteúdo -->
</kickoff_redator>
```

---

### Seções Obrigatórias

#### 1. METADADOS PROCESSUAIS

```xml
<processo>
  <numero>0000000-00.0000.0.00.0000</numero>
  <orgao>Tribunal de Justiça de Santa Catarina - 1ª Câmara Criminal</orgao>
  <natureza>Apelação Criminal | RESE | Agravo | etc</natureza>
</processo>
```

**Campos obrigatórios:**

- `numero`: Número CNJ do processo
- `orgao`: Tribunal e câmara/turma
- `natureza`: Tipo do recurso

---

#### 2. PARTES

```xml
<partes>
  <recorrente>João da Silva</recorrente>
  <recorrido>Ministério Público do Estado de Santa Catarina</recorrido>
  <advogado_recorrente>Dr. Fulano de Tal - OAB/SC 12345</advogado_recorrente>
  <advogado_recorrido>Procurador de Justiça Ciclano - matrícula 67890</advogado_recorrido>
</partes>
```

**Campos obrigatórios:**

- `recorrente`: Nome completo
- `recorrido`: Nome completo (pode ser "Ministério Público")
- `advogado_recorrente`: Nome + OAB
- `advogado_recorrido`: Nome + OAB/matrícula (se aplicável)

---

#### 3. TIPO DE PEÇA

```xml
<tipo_peca>voto</tipo_peca>
```

**Valores aceitos:**

- `voto` (padrão)
- `sentenca`
- `decisao_monocratica`

---

#### 4. MODO JÚRI (condicional obrigatório)

```xml
<banner_modo_juri enabled="true">
  <crime_base>Homicídio qualificado - Art. 121, §2º, incisos I e IV, CP</crime_base>
  <orientacao>Usar linguagem de prelibação: "elementos indicam", "há indícios de", "aparenta configurar". NUNCA usar afirmações categóricas de autoria ou materialidade ("matou", "cometeu", "é autor").</orientacao>
</banner_modo_juri>
```

**Quando usar:**

- `enabled="true"`: Crime doloso contra a vida (competência do Júri)
- `enabled="false"`: Demais casos

**Campos quando enabled="true":**

- `crime_base`: Descrição do crime (obrigatório)
- `orientacao`: Orientação linguística (obrigatório)

---

#### 5. ESTRUTURA ESPERADA (NOVO em v5.2)

```xml
<estrutura_esperada>
  <tem_preliminares>true</tem_preliminares>
  <preliminares>
    <item>Nulidade por cerceamento de defesa</item>
    <item>Incompetência do juízo</item>
  </preliminares>
  <tem_dosimetria>true</tem_dosimetria>
  <numeracao>hierarquica</numeracao>
  <exemplo_estrutura>
    <![CDATA[
I. RELATÓRIO
II. VOTO
   1. PRELIMINARES
      1.1. Nulidade por cerceamento de defesa
      1.2. Incompetência do juízo
   2. MÉRITO
      2.1. Tese de absolvição
      2.2. Desclassificação
   3. DOSIMETRIA
      3.1. Primeira fase
      3.2. Segunda fase
      3.3. Terceira fase
III. DISPOSITIVO
    ]]>
  </exemplo_estrutura>
</estrutura_esperada>
```

**Campos obrigatórios:**

- `tem_preliminares`: boolean (true/false)
- `preliminares`: array (vazio se tem_preliminares=false)
- `tem_dosimetria`: boolean
- `numeracao`: "hierarquica" (padrão) | "flat"
- `exemplo_estrutura`: Template visual da estrutura esperada

**Propósito:** Orientar Redator sobre estrutura hierárquica do voto (1., 1.1, 1.2, etc)

---

#### 6. SÍNTESE DO CASO

```xml
<sintese_caso>
  <![CDATA[
João da Silva, condenado em 1ª instância a 8 anos de reclusão por roubo majorado (art. 157, §2º, I e II, CP), interpõe apelação alegando: (1) nulidade por cerceamento de defesa ao indeferir testemunha de alibi, (2) absolvição por insuficiência probatória, (3) dosimetria excessiva. O Ministério Público, em contrarrazões, rebate sustentando regularidade processual e robustez do conjunto probatório.
  ]]>
</sintese_caso>
```

**Formato:** CDATA com narrativa concisa (150-300 palavras)

**Conteúdo:**

- Quem são as partes
- Qual a decisão recorrida
- Quais os pedidos no recurso
- Posicionamento do recorrido (se houver)

---

#### 7. FUNDAMENTOS (CORE DO VOTO)

Estrutura de teses é o núcleo informativo do Handoff.

```xml
<fundamentos>

  <tese id="1">
    <titulo>Nulidade por cerceamento de defesa</titulo>
    <conclusao>PROCEDENTE | PARCIALMENTE_PROCEDENTE | IMPROCEDENTE</conclusao>

    <fundamentacao_legal>
      <item>Art. 564, III, c/c art. 93, IX, CPP</item>
      <item>Súmula 523 do STF</item>
    </fundamentacao_legal>

    <provas_relevantes>
      <prova id="P01">
        <descricao>Requerimento de defesa solicitando oitiva de testemunha João Santos (fls. 80-82 / evento 6)</descricao>
        <localizacao>fls. 80-82 / evento 6</localizacao>
        <relevancia>Defesa sustenta que testemunha comprovaria alibi do réu no momento do crime</relevancia>
      </prova>
      <prova id="P02">
        <descricao>Decisão de indeferimento (fls. 95 / evento 7)</descricao>
        <localizacao>fls. 95 / evento 7</localizacao>
        <relevancia>Juízo indeferiu sob fundamento de intempestividade do rol</relevancia>
      </prova>
    </provas_relevantes>

    <provas_orais_detalhadas>
      <!-- OPCIONAL: usar apenas se transcrição disponível -->
      <depoimento id="P05">
        <depoente>Testemunha Maria Souza (vítima)</depoente>
        <localizacao>fls. 120-122 / evento 8</localizacao>
        <trechos_relevantes>
          <trecho>"Reconheci o réu, ele apontou a arma e levou minha bolsa"</trecho>
          <trecho>"Ele estava de capacete, mas vi o rosto quando tirou"</trecho>
        </trechos_relevantes>
        <analise>Depoimento apresenta contradição interna (capacete vs. viu rosto), mas foi confirmado em reconhecimento fotográfico e pessoal. Peso probatório médio.</analise>
      </depoimento>
    </provas_orais_detalhadas>

    <tensoes_probatorias>
      <tensao>P01 (requerimento de defesa) vs P02 (indeferimento): Defesa não demonstrou qual informação específica testemunha traria que não poderia ser suprida por outros meios</tensao>
    </tensoes_probatorias>

    <jurisprudencias>
      <caso>
        <identificacao>STJ, HC 123456, Rel. Min. Fulano, j. 15/03/2023, 6ª Turma</identificacao>
        <ementa_resumida>Cerceamento de defesa configura-se apenas com demonstração de efetivo prejuízo (pas de nullité sans grief). Indeferimento de testemunha sem demonstração de prejuízo concreto não enseja nulidade.</ementa_resumida>
        <aplicacao>Aplicável ao caso porque defesa não demonstrou qual informação específica a testemunha traria. Ausência de prejuízo concreto afasta nulidade.</aplicacao>
      </caso>
    </jurisprudencias>

    <ratio_decidendi>
      <![CDATA[
O cerceamento de defesa, para ensejar nulidade processual (art. 564, III, CPP), exige demonstração de efetivo prejuízo à parte, conforme princípio pas de nullité sans grief. No caso concreto, embora a defesa tenha requerido oitiva de testemunha para comprovar alibi (P01), o indeferimento (P02) fundamentou-se em intempestividade do rol. Ainda que se pudesse questionar o rigor formal, a defesa não demonstrou, em momento algum, qual informação específica a testemunha traria que não poderia ser suprida por outros meios probatórios. A jurisprudência do STJ é firme no sentido de que a ausência de demonstração de prejuízo concreto afasta a nulidade (STJ, HC 123456). Assim, não se verifica cerceamento de defesa no caso concreto.
      ]]>
    </ratio_decidendi>

    <argumentos_recorrente>
      <argumento>Defesa requereu oitiva de testemunha João Santos para comprovar alibi</argumento>
      <argumento>Indeferimento foi injustificado, pois testemunha era essencial</argumento>
      <argumento>Violação ao princípio do contraditório e ampla defesa</argumento>
    </argumentos_recorrente>

    <contra_argumentos>
      <argumento>MP sustenta que rol de testemunhas foi intempestivo (apresentado após prazo legal)</argumento>
      <argumento>MP argumenta que ônus era da defesa demonstrar prejuízo concreto, o que não ocorreu</argumento>
    </contra_argumentos>

  </tese>

  <tese id="2">
    <!-- Repetir estrutura -->
  </tese>

  <dosimetria>
    <!-- OPCIONAL: usar apenas se caso envolver dosimetria -->
    <primeira_fase>
      <circunstancias_judiciais>
        <culpabilidade>Normal ao tipo, sem elementos de maior censurabilidade</culpabilidade>
        <antecedentes>Réu é reincidente (condenação transitada em julgado em 2020 por furto - fls. 200)</antecedentes>
        <conduta_social>Não há elementos nos autos</conduta_social>
        <personalidade>Sentença menciona "personalidade voltada ao crime", mas sem fundamentação concreta. Expressão genérica.</personalidade>
        <motivos>Motivo patrimonial, comum ao tipo</motivos>
        <circunstancias>Crime praticado em via pública, durante o dia, sem peculiaridades</circunstancias>
        <consequencias>Vítima sofreu lesões leves e trauma psicológico</consequencias>
        <comportamento_vitima>Não contribuiu para o crime</comportamento_vitima>
      </circunstancias_judiciais>
      <pena_base_calculada>6 anos de reclusão (sentença fixou, mas fundamentação genérica em "personalidade" é questionável)</pena_base_calculada>
      <analise_critica>Fundamentação da sentença em "personalidade voltada ao crime" é genérica e não atende ao requisito de fundamentação idônea exigido pelo STJ (Súmula 444). Possível redução para pena-base mínima (4 anos).</analise_critica>
    </primeira_fase>

    <segunda_fase>
      <agravantes>
        <item>Nenhuma</item>
      </agravantes>
      <atenuantes>
        <item>Nenhuma</item>
      </atenuantes>
      <pena_intermediaria>6 anos de reclusão (mantida)</pena_intermediaria>
    </segunda_fase>

    <terceira_fase>
      <causas_aumento>
        <item>Art. 157, §2º, I (emprego de arma): aumentada em 1/2</item>
        <item>Art. 157, §2º, II (concurso de pessoas): aumentada em 1/2</item>
      </causas_aumento>
      <causas_diminuicao>
        <item>Nenhuma</item>
      </causas_diminuicao>
      <pena_definitiva>12 anos de reclusão (sentença aplicou fração de 1/2 para concurso de duas majorantes)</pena_definitiva>
      <analise_critica>Fração de 1/2 está dentro do razoável para concurso de duas majorantes. Jurisprudência aceita frações entre 1/3 e 2/3.</analise_critica>
    </terceira_fase>

    <regime_inicial>
      <regime>Fechado</regime>
      <fundamentacao>Pena superior a 8 anos + reincidência justificam regime inicial fechado (art. 33, §2º, "a", CP). Sentença fundamentou adequadamente.</fundamentacao>
    </regime_inicial>
  </dosimetria>

</fundamentos>
```

**Estrutura de `<tese>`:**

- `id`: número sequencial (obrigatório)
- `titulo`: nome da tese (obrigatório)
- `conclusao`: PROCEDENTE / PARCIALMENTE_PROCEDENTE / IMPROCEDENTE (obrigatório)
- `fundamentacao_legal`: array de artigos/súmulas (obrigatório)
- `provas_relevantes`: array de provas (obrigatório)
- `provas_orais_detalhadas`: array de depoimentos (OPCIONAL, usar se transcrição disponível)
- `tensoes_probatorias`: array de contradições (opcional)
- `jurisprudencias`: array de precedentes (obrigatório se houver)
- `ratio_decidendi`: CDATA com núcleo do raciocínio jurídico (OBRIGATÓRIO)
- `argumentos_recorrente`: array (obrigatório)
- `contra_argumentos`: array (obrigatório)

**Estrutura de `<dosimetria>` (opcional):**

- Usar apenas se caso envolver dosimetria
- Detalhar 3 fases + regime inicial
- Incluir `analise_critica` quando fundamentação da sentença for questionável

---

#### 8. CONTEXTO PROCESSUAL (OPCIONAL)

```xml
<contexto_processual>
  <![CDATA[
Réu está preso cautelarmente desde 01/01/2023. Prescrição da pretensão punitiva ocorrerá em 15/06/2025 (12 anos da data do fato). Caso relevante para análise da prisão cautelar em eventual habeas corpus.
  ]]>
</contexto_processual>
```

**Quando usar:**

- Informações críticas sobre prisão cautelar, prescrição, medidas cautelares
- Histórico de recursos (embargos, agravos pendentes)
- Questões de competência ou conexão

**Quando NÃO usar:**

- Casos simples sem peculiaridades processuais
- Economia de tokens: preencher APENAS quando necessário

---

#### 9. PECULIARIDADES (OPCIONAL)

```xml
<peculiaridades>
  <![CDATA[
Caso teve repercussão midiática local. Vítima é figura pública (vereador municipal). Atenção à linguagem técnica e objetiva para evitar interpretações tendenciosas.
  ]]>
</peculiaridades>
```

**Quando usar:**

- Repercussão midiática, social ou política
- Particularidades culturais ou regionais
- Aspectos que impactam escolhas linguísticas

**Quando NÃO usar:**

- Casos ordinários sem peculiaridades

---

#### 10. SENSIBILIDADES (OPCIONAL)

```xml
<sensibilidades>
  <nivel>ALTA</nivel>
  <descricao>
    <![CDATA[
Caso envolve crime sexual contra vulnerável (vítima de 12 anos). Atenção à linguagem respeitosa e técnica ao descrever os fatos. Evitar detalhamento desnecessário de atos libidinosos. Priorizar termos técnicos ("conjunção carnal", "ato libidinoso") em detrimento de expressões coloquiais.
    ]]>
  </descricao>
</sensibilidades>
```

**Níveis:**

- `NENHUMA`: Caso ordinário
- `MEDIA`: Envolve temas delicados mas não extremos
- `ALTA`: Crimes sexuais, violência doméstica, vítimas vulneráveis

**Quando usar:**

- Crimes contra dignidade sexual
- Violência doméstica
- Vítimas crianças/adolescentes
- Casos com impacto emocional significativo

**Quando NÃO usar:**

- Crimes patrimoniais ordinários
- Casos sem sensibilidades específicas

---

#### 11. ESCOPO

```xml
<escopo>
  <![CDATA[
Redigir voto estruturado com:
- I. RELATÓRIO (síntese processual concisa, 200-300 palavras)
- II. VOTO (análise fundamentada de cada tese com numeração hierárquica 1., 1.1, 1.2)
- III. DISPOSITIVO (canônico, sem alterações)

Atenção especial:
- Fundamentar cuidadosamente Tese 2 (absolvição), pois jurisprudência divergente requer análise minuciosa
- Tese 3 (dosimetria): criticar fundamentação genérica da sentença sobre "personalidade"
- Modo Júri NÃO ativado (crime patrimonial), usar linguagem assertiva normal
  ]]>
</escopo>
```

**Formato:** CDATA com orientações específicas de redação

**Conteúdo:**

- Estrutura esperada (sempre)
- Pontos de atenção específicos
- Orientações de linguagem
- Ênfases necessárias

---

#### 12. DISPOSITIVO

```xml
<dispositivo>
  <![CDATA[
Nego provimento ao recurso.
  ]]>
</dispositivo>
```

**Regra CRÍTICA:** Texto EXATO que constará no voto. Redator DEVE copiar sem alterações.

**Formato:** CDATA com texto canônico

**Exemplos válidos:**

- "Nego provimento ao recurso."
- "Dou parcial provimento ao recurso."
- "Dou provimento ao recurso para absolver o réu."
- "Declaro extinta a punibilidade pela prescrição."

---

#### 13. NÃO FAZER

```xml
<nao_fazer>
  <item>Não produzir ementa</item>
  <item>Não alterar dispositivo</item>
  <item>Não copiar integralmente sentença ou petições</item>
  <item>Não fazer afirmações factuais sem rastreabilidade a provas</item>
  <item>Não citar jurisprudência sem identificação mínima (Tribunal + número)</item>
</nao_fazer>
```

**Checklist negativo obrigatório**

---

#### 14. ESTIMATIVAS

```xml
<estimativas>
  <tempo_redacao>50 minutos</tempo_redacao>
  <complexidade>BAIXA | MEDIA | ALTA</complexidade>
  <sensibilidade>NENHUMA | MEDIA | ALTA</sensibilidade>
</estimativas>
```

---

#### 15. ANEXOS

```xml
<anexos>
  <blueprint>
    <localizacao>Mesma sessão do Analista / Document ID 12345</localizacao>
    <resumo>Blueprint completo com quadro probatório catalogado (P01-P08), análise de 3 teses, jurisprudências do STJ incorporadas, e estratégia de voto detalhada</resumo>
  </blueprint>
</anexos>
```

---

## EXEMPLO COMPLETO

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kickoff_redator version="5.2">

  <processo>
    <numero>0000000-00.2023.8.24.0000</numero>
    <orgao>Tribunal de Justiça de Santa Catarina - 1ª Câmara Criminal</orgao>
    <natureza>Apelação Criminal</natureza>
  </processo>

  <partes>
    <recorrente>João da Silva</recorrente>
    <recorrido>Ministério Público do Estado de Santa Catarina</recorrido>
    <advogado_recorrente>Dr. Fulano de Tal - OAB/SC 12345</advogado_recorrente>
    <advogado_recorrido>Procurador de Justiça Ciclano Santos</advogado_recorrido>
  </partes>

  <tipo_peca>voto</tipo_peca>

  <banner_modo_juri enabled="false"/>

  <estrutura_esperada>
    <tem_preliminares>true</tem_preliminares>
    <preliminares>
      <item>Nulidade por cerceamento de defesa</item>
    </preliminares>
    <tem_dosimetria>true</tem_dosimetria>
    <numeracao>hierarquica</numeracao>
    <exemplo_estrutura>
      <![CDATA[
I. RELATÓRIO
II. VOTO
   1. PRELIMINARES
      1.1. Nulidade por cerceamento de defesa
   2. MÉRITO
      2.1. Absolvição por insuficiência probatória
   3. DOSIMETRIA
      3.1. Primeira fase
      3.2. Segunda fase
      3.3. Terceira fase
III. DISPOSITIVO
      ]]>
    </exemplo_estrutura>
  </estrutura_esperada>

  <sintese_caso>
    <![CDATA[
João da Silva, condenado a 8 anos de reclusão por roubo majorado (art. 157, §2º, I e II, CP), interpõe apelação alegando: (1) nulidade por cerceamento de defesa, (2) absolvição por insuficiência probatória, (3) dosimetria excessiva. MP rebate sustentando regularidade processual e robustez probatória.
    ]]>
  </sintese_caso>

  <fundamentos>

    <tese id="1">
      <titulo>Nulidade por cerceamento de defesa</titulo>
      <conclusao>IMPROCEDENTE</conclusao>

      <fundamentacao_legal>
        <item>Art. 564, III, CPP</item>
        <item>Princípio pas de nullité sans grief</item>
      </fundamentacao_legal>

      <provas_relevantes>
        <prova id="P01">
          <descricao>Requerimento de defesa (fls. 80-82 / evento 6)</descricao>
          <localizacao>fls. 80-82 / evento 6</localizacao>
          <relevancia>Solicitava oitiva de testemunha para comprovar alibi</relevancia>
        </prova>
        <prova id="P02">
          <descricao>Decisão de indeferimento (fls. 95 / evento 7)</descricao>
          <localizacao>fls. 95 / evento 7</localizacao>
          <relevancia>Indeferiu sob fundamento de intempestividade</relevancia>
        </prova>
      </provas_relevantes>

      <tensoes_probatorias>
        <tensao>Defesa não demonstrou prejuízo concreto do indeferimento</tensao>
      </tensoes_probatorias>

      <jurisprudencias>
        <caso>
          <identificacao>STJ, HC 123456, Rel. Min. Fulano, j. 15/03/2023, 6ª Turma</identificacao>
          <ementa_resumida>Cerceamento exige demonstração de prejuízo concreto</ementa_resumida>
          <aplicacao>Aplicável porque defesa não demonstrou prejuízo</aplicacao>
        </caso>
      </jurisprudencias>

      <ratio_decidendi>
        <![CDATA[
Cerceamento de defesa exige demonstração de efetivo prejuízo (pas de nullité sans grief). Defesa não demonstrou qual informação específica testemunha traria que não poderia ser suprida por outros meios. Ausência de prejuízo concreto afasta nulidade, conforme STJ, HC 123456.
        ]]>
      </ratio_decidendi>

      <argumentos_recorrente>
        <argumento>Testemunha era essencial para alibi</argumento>
        <argumento>Indeferimento violou ampla defesa</argumento>
      </argumentos_recorrente>

      <contra_argumentos>
        <argumento>Rol intempestivo</argumento>
        <argumento>Ausência de demonstração de prejuízo</argumento>
      </contra_argumentos>

    </tese>

  </fundamentos>

  <contexto_processual>
    <![CDATA[
Réu preso cautelarmente desde 01/01/2023. Prescrição em 15/06/2025.
    ]]>
  </contexto_processual>

  <escopo>
    <![CDATA[
Redigir voto estruturado com numeração hierárquica (1., 1.1, 1.2). Fundamentar cuidadosamente Tese 1 com jurisprudência do STJ.
    ]]>
  </escopo>

  <dispositivo>
    <![CDATA[
Nego provimento ao recurso.
    ]]>
  </dispositivo>

  <nao_fazer>
    <item>Não produzir ementa</item>
    <item>Não alterar dispositivo</item>
    <item>Não copiar integralmente sentença</item>
    <item>Não fazer afirmações sem rastreabilidade</item>
  </nao_fazer>

  <estimativas>
    <tempo_redacao>50 minutos</tempo_redacao>
    <complexidade>MEDIA</complexidade>
    <sensibilidade>NENHUMA</sensibilidade>
  </estimativas>

  <anexos>
    <blueprint>
      <localizacao>Mesma sessão Analista</localizacao>
      <resumo>Blueprint com provas P01-P05, jurisprudências STJ</resumo>
    </blueprint>
  </anexos>

</kickoff_redator>
```

---

## VALIDAÇÃO

Handoff XML deve ser validado antes de enviar ao Redator:

```bash
xmllint --schema handoff_v5.2.xsd handoff.xml
```

**Campos obrigatórios mínimos:**

- `processo` (completo)
- `partes` (completo)
- `tipo_peca`
- `banner_modo_juri`
- `estrutura_esperada`
- `sintese_caso`
- `fundamentos` (pelo menos 1 tese)
- `escopo`
- `dispositivo`
- `nao_fazer`
- `estimativas`
- `anexos`

**Campos opcionais (preencher apenas quando necessário):**

- `provas_orais_detalhadas` (dentro de `<tese>`)
- `dosimetria`
- `contexto_processual`
- `peculiaridades`
- `sensibilidades`

---

## REGRAS DE OURO

1. **Autossuficiência:** Redator deve conseguir trabalhar APENAS com Handoff + Blueprint
2. **IDs de prova:** Sempre referenciar P01, P02, etc
3. **Ratio decidendi explícita:** Núcleo do raciocínio jurídico em cada tese
4. **Dispositivo canônico:** Texto EXATO, sem alterações
5. **Campos opcionais:** Economia inteligente (preencher apenas quando agregar valor)
6. **Estrutura esperada:** Sempre especificar numeração hierárquica
7. **Modo Júri:** Ativar banner se crime doloso contra a vida

---

**Handoff v5.2 pronto para uso.**
