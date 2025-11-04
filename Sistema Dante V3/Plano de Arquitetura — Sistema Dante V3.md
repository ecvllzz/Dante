# Plano de Arquitetura — Sistema Dante V3

## 0) Propósito e princípios

**Missão**: apoiar a elaboração de votos criminais de 2º grau — do recebimento dos autos à ementa — com **fidelidade estrita aos autos**, **excelência técnico-jurídica**, **linguagem adequada** e **controle anti-plágio**.

**Princípios invariantes (valem para todos os agentes):**

- **Forma do Voto**: Relatório → Fundamentação → Dispositivo. (**Ementa** é gerada por agente próprio, ao final.)

- **Estilo**: português jurídico brasileiro formal; **sem travessões (—)**; números **em algarismos**; evitar advérbios de ênfase (“claramente”, “indubitavelmente”); citações com **mais de 2 linhas** em bloco com recuo; neutralidade valorativa; precisão terminológica.

- **Fidelidade**: todo enunciado fático relevante deve ter **lastro nos autos** (compromisso interno do sistema). Na **Blueprint**, trazer **extratos essenciais** (entre aspas) **apenas** quando necessários à clareza, sem “poluir”.

- **Jurisprudência (política de pesquisa)**: foco em **STJ** e **TJSC**; **STF**, secundário. A pesquisa **aparece no Diálogo Estratégico**; **não** é citada na Blueprint; **no Voto**, eventuais citações são livres ao Redator; **o padrão mínimo por tese é verificado apenas pelo Revisor**.

- **Preliminares e Dosimetria**: **somente** se houver insurgência recursal pertinente.

- **Dispositivo — fórmula canônica**:  
  **“Isto posto, voto no sentido de [conhecer / não conhecer / conhecer parcialmente] o recurso interposto e [prover / desprover / prover parcialmente] o recurso.”**

- **Excesso de linguagem (Júri/Pronúncia – RESE)**: quando aplicável, **vedada** linguagem de certeza sobre autoria; usar fórmulas de **prelibação** (“indícios suficientes de autoria”, “lastro probatório apto à submissão ao Conselho de Sentença”); conclusão **clara** sem afirmar autoria como certeza.

---

## 1) Agentes e papéis

### 1.1 [D] Maestro (Gemini — System)

**Papel**: **governança global** e **orquestração**. Não lê autos, não decide nada de caso concreto, não “detecta” rito; apenas declara **políticas invariantes** (estilo, forma, anti-plágio, foco STJ/TJSC, ementa separada, dispositivo canônico) e **coordena o fluxo**.

**Protocolo de ativação (mensagens de “OK”):**

1. Enviar **System do [D] Maestro** + “Memória do Gabinete” em anexo → resposta: **“OK — Maestro carregado.”**

2. Enviar **System do [D] Analista** (sem anexos) → resposta: **“OK — Analista pronto.”**

3. Enviar **Kickoff mínimo** (lista de documentos) **+ anexos** → quem assume é **[D] Analista**, iniciando Etapa A.

> **Obs.**: Toda lógica contextual (Saneamento, mapeamento de PROVAS, condicionalização de seções, modo Júri, análise por tese, Blueprint) pertence **exclusivamente** ao **[D] Analista**.

---

### 1.2 [D] Analista (Gemini)

**Papel**: ler os autos, **mapear PROVAS**, conduzir o **Diálogo Estratégico** (com pesquisa STJ/TJSC; STF secundário) e gerar a **Blueprint** maximalista (auto-suficiente), além do **Handoff** para o Redator.

**Entradas**:

- Mínimo: **Sentença.pdf**, **Recurso.pdf**, **Contrarrazões.pdf**, **Parecer.pdf**.

- Opcionais: inquérito, laudos, depoimentos, jurisprudência/doutrina juntadas aos autos, decisões correlatas etc.

**Saídas**:

- (A) **Esboço para Diálogo Estratégico**.

- (B) **Minuta ajustada** após o Diálogo.

- (C) **Blueprint final + Handoff** para o Redator.

**Operação detalhada:**

**Passo 0 — Saneamento (rápido, antes de tudo)**

- Verificar **OCR/legibilidade**: “ok” ou listar **páginas afetadas** (doc/páginas), **impacto** (qual tese pode ser comprometida) e, se necessário, **sugerir reenvio**.

- Responder: “**Saneamento**: OK / pendências X-Y (impacto Z).”

**Fase A — Esboço para Diálogo Estratégico**

1. **Síntese fático-processual** (cronológica, objetiva).

2. **Mapa de PROVAS** (único mapeamento formal):
   
   - `PROVA-01 | Doc: <nome> | p. X–Y | Tipo: <depoimento/laudo/...> | Extrato: “...” | Relevância: <para qual questão/tese>`
   
   - **Nota**: PROVAS devem refletir **o que consta nas peças**; foco especial na **Sentença** (fundamentos e provas).

3. **Análise inicial das teses recursais** (em **ordem de prejudicialidade**, quando couber). Para **cada tese**:
   
   - **(1) Alegação recursal** (síntese fiel e precisa).
   
   - **(2) Análise jurídica inicial** (sem citar pesquisa externa ainda).
   
   - **(3) Confronto com o conjunto probatório** (referir **PROVA-xx** pertinentes; extratos só quando imprescindíveis).
   
   - **(4) Pesquisa Estratégica (STJ/TJSC; STF secundário)**: **subtópico dentro da tese** com **achados para debate** (títulos/teses/chaves; links quando possível). **Sem** inserir citação na Blueprint.
   
   - **(5) Direção provável** (acolher/rejeitar/ajustes necessários).
   
   - **(6) Pontos para discussão** (ambiguidade, lacunas probatórias, distinções possíveis, riscos).

**Fase B — Diálogo Estratégico (interativo)**

- Validar leitura das provas e da Sentença, ponderar achados STJ/TJSC (STF secundário), discutir *distinguishing*, calibrar **modalização por tese** (de acordo com robustez da prova e solidez do direito), e fechar **encaminhamentos**.

- **Resultado esperado**: uma minuta de entendimento **pactuada** (sem formalismo excessivo), que o Analista converterá em Blueprint.

**Fase C — Blueprint Maximalista (auto-suficiente e fluida)**

- **RELATÓRIO**
  
  - **Síntese fático-processual** (objetiva, sem adjetivação).
  
  - **Teses Recursais (Resumo objetivo)**.
  
  - **Contrarrazões e Parecer** (posições gerais).

- **FUNDAMENTAÇÃO**
  
  1. **Juízo de admissibilidade**: **apenas** se houver insurgência pertinente; se não, uma linha (“Conheço do recurso”).
  
  2. **Preliminares** (se houver). Para cada uma:  
     **A.** Argumentos do Recorrente; **B.** Fundamentos da Sentença; **C.** Extratos fatuais cruciais (**PROVA-xx** + trechos entre aspas quando indispensáveis); **D.** **Parágrafos-chave sugeridos** (raciocínio jurídico **fluido**, dialogando com sentença e recurso **dentro** da tese); **E.** Proposta (acolher/rejeitar).
  
  3. **Mérito** (mesma estrutura **A–E** por tese).
  
  4. **Dosimetria** (apenas se houver insurgência específica).

- **SÍNTESE PARA O DISPOSITIVO**: resultados por tese + resultado final agregado (provimento/desprovimento/parcial).

- **ANEXO opcional**: Quadro de PROVAS.

- **MODO JÚRI (quando for o caso)**: a primeira linha da Blueprint traz um **banner técnico** discreto — “**[MODO JÚRI/PRONÚNCIA ATIVADO]**” — apenas para orientar Redator/Revisor (sem alterar o tom do texto).

**Handoff do Analista → Redator (Claude)**

- **Objetivo**: “Redigir Voto **fiel à Blueprint**. **Não criar fatos**. Melhorias **apenas em direito** (jurisprudência, doutrina, estilo) a partir do **repositório do Redator**, **apenas para estilo, autoridade e entendimento a respeito do direito**.”

- **Estrutura**: Relatório → Fundamentação → Dispositivo (fórmula canônica). **Não produzir ementa.**

- **Modo Júri**: se banner ativo, lembrar vedação a excesso de linguagem.

- **Anti-plágio**: reelaboração analítica, sem replicar ipsis litteris trechos de Sentença/Peças.

- **Checklist de saída (conciso)**: fidelidade; coesão; dispositivo canônico; sem ementa; (se houver) modo Júri respeitado.

---

### 1.3 [D] Redator (Claude)

**Papel**: redigir o **Voto** com base na Blueprint; **pode** enriquecer o **direito** com jurisprudência/doutrina/modelos do **repositório do Redator**; **não** cria fatos.

**Entradas**: Blueprint + Handoff; repositório do Redator (1–2 documentos com jurisprudência/doutrina/exemplos de tópicos).  
**Saída**: Voto completo (sem ementa), com **dispositivo** na fórmula canônica.

**Comportamentos-chave:**

- **Fidelidade total** à Blueprint; **nada** de extrapolar fatos.

- **Modalização por tese**:
  
  - Prova robusta / direito pacífico → conclusão **assertiva** (p.ex., “é de rigor…”).
  
  - Prova controvertida → linguagem **contida** (“indicam/ autorizam concluir”), com **conclusão clara** ao final.
  
  - **Modo Júri** (quando aplicável) → **vedado** excesso de linguagem sobre autoria.

- **Auto-checagem antes de entregar**: fidelidade; coesão e progressão lógica; estilo do gabinete; dispositivo canônico; **sem ementa**.

---

### 1.4 [D] Revisor (Gemini) — mesma sessão do Analista

**Papel**: **Worthy Opponent** (auditoria técnica adversarial), diálogo e **reescrita** final do Voto, com marcação das alterações.

**Entradas**: Voto (após crivo humano), os **mesmos autos** e a **Blueprint** (na mesma sessão do Analista).  
**Saídas**: (A) **Relatório de Análise** → diálogo → (B) **Voto reescrito** (alterações em **negrito**; **uma linha em branco** entre parágrafos).

**Relatório de Análise — estrutura obrigatória**  
I. **Fidelidade, Conformidade e Originalidade**

- (A) Aderência à Blueprint e aos autos (ordem de teses, coerência probatória).

- (B) Cobertura exaustiva das teses (omissões?).

- (C) Precisão fática/probatória (verificação pontual de referências, trechos de depoimentos/laudos).

- (D) **Originalidade**: detecção de paralelismo com Sentença/Parecer; **exigir reelaboração**.

II. **Autoridade Jurídica (Juris/Doutrina)**

- **Padrão**: por **tese**, verificar a **existência de ao menos 1 citação jurisprudencial** (preferência **STJ/TJSC**; STF quando fizer sentido). **Se inviável**, cobrar **1 doutrinária**.

- **Elevação a “crítica”**: quando a tese **depender essencialmente** de autoridade (sem a qual a conclusão fica vulnerável).

III. **Qualidade da Redação Jurídica**

- Coesão e progressão lógica; neutralidade; precisão terminológica.

- **Checklists obrigatórios**: sem travessões; sem “5 (cinco)”; evitar advérbios de ênfase; citações longas em bloco; **modo Júri** (se aplicável).

IV. **Análise Adversarial Tese a Tese**

- Crítica da fundamentação; vulnerabilidades; **nota 0–10** por tese; sugestões cirúrgicas (o que incluir/ajustar/cortar/citar).

V. **Orientações de Reescrita**

- Lista **acionável** e objetiva de ajustes a integrar.

**Fase B — Reescrita Final (saída formal)**

- Integrar **tudo** o que foi acertado no diálogo; **destacar em negrito** todo trecho **alterado/adicionado**; **um espaço em branco** entre parágrafos.

- **Auto-verificação**: consistência lógica; estilo; fidelidade; ausência de novas inconsistências.

- **Não produzir ementa.**

> **Notas operacionais (Revisor):**  
> • **Dosimetria**: só auditar quando houver insurgência específica — **cheque rápido** (bis in idem; art. 59 CP com fundamentação concreta; súmulas sensíveis).  
> • **Prescrição**: opcional, apenas se emergir no diálogo (cheque breve; sem emphasis desnecessária).

---

### 1.5 [D] Ementa (Gemini)

**Papel**: gerar **apenas a Ementa**, fiel ao Voto final, seguindo **regras estritas** do seu comando “[D] EMENTA V2.md”.

**Entradas**: Voto final (já revisado).  
**Saída**: Ementa em **caixa alta**, segmentada por **tese** (Preliminar(es), Mérito, Dosimetria — **apenas** se existirem no Voto), e **linha final** com o **resultado**; **sem inovação** de conteúdo.

---

## 2) Fluxo operacional (fim-a-fim, no webapp)

1. **Carregar políticas**
   
   - Enviar **[D] Maestro (System)** + “Memória do Gabinete” → “OK — Maestro carregado.”

2. **Armar análise**
   
   - Enviar **[D] Analista (System)** → “OK — Analista pronto.”

3. **Kickoff**
   
   - Enviar **lista de documentos** + **anexos** → [D] Analista: “Recebido. Iniciando Etapa A: Saneamento → Esboço.”

4. **Fase A** (Analista)
   
   - Saneamento → Síntese → Mapa de PROVAS → Teses (com subtópico de Pesquisa Estratégica **por tese**).

5. **Fase B** (Diálogo Estratégico)
   
   - Ajustes finos; pactuar raciocínios e calibrar modalização por tese; decidir ativação de **Modo Júri** quando aplicável.

6. **Fase C** (Blueprint + Handoff)
   
   - Blueprint **maximalista e fluida** + Handoff claro (sem exigir citação mínima).

7. **Redação** (Redator/Claude)
   
   - Voto fiel à Blueprint; melhorias **apenas em direito** (repositório do Redator). **Sem ementa.**

8. **Revisão** (Revisor/Gemini)
   
   - Relatório **Worthy Opponent** → diálogo → **reescrita** com **negrito** no que mudou e **linha em branco** entre parágrafos; cheques rápidos; **sem ementa**.

9. **Ementa** (Agente próprio)
   
   - Gera ementa estrita, **espelhando** o Voto final.

---

## 3) Artefatos lógicos e regras de conteúdo

- **PROVAS (único mapeamento formal)**: etiquetas simples e legíveis (`PROVA-01`, `PROVA-02`…), com **doc/pág/trecho** e **relevância**.

- **Pesquisa estratégica (STJ/TJSC; STF secundário)**: **dentro de cada tese** (no Esboço/Diálogo), **sem aparecer** na Blueprint.

- **Blueprint**: documento **auto-suficiente** para o Redator; **máxima utilidade**, **fluidez** e **economia** (referir peças **apenas** quando clareia a análise).

- **Handoff**: reforça **limites** (sem criar fatos; modo Júri quando aplicável; dispositivo canônico; sem ementa).

- **Voto (Redator)**: fidelidade + qualidade redacional; **citações livres** (do repositório) — **sem obrigatoriedade** no prompt.

- **Revisor**: único responsável por cobrar **pelo menos 1 citação por tese** (STJ/TJSC preferencial; STF quando couber; se inviável, doutrina).

- **Ementa**: formato e conteúdo estritos ao Voto.

---

## 4) Modo Júri/Pronúncia — controles específicos

- **Gatilho**: identificado pelo **Analista** (natureza do recurso/peça).

- **Efeitos**:
  
  - **Léxico bloqueado**: vedado afirmar autoria como certeza; proibir rótulos como “autor do crime”, “comprovada a autoria”, etc.
  
  - **Linguagem adequada**: “indícios suficientes de autoria”, “prova da materialidade”, “lastro probatório apto à submissão ao Conselho de Sentença”.
  
  - **Banner técnico** no topo da **Blueprint**: “**[MODO JÚRI/PRONÚNCIA ATIVADO]**”.
  
  - **Redator**: mantém conclusão clara **sem excesso de linguagem**.
  
  - **Revisor**: checa estritamente a vedação.

---

## 5) Qualidade, riscos e “Definition of Done”

**Critérios de aceite (DoD):**

- **Fidelidade**: Voto compatível com a Blueprint e com os autos; sem extrapolar fatos.

- **Cobertura**: todas as teses do recurso **enfrentadas**; sem omissões materiais.

- **Autoridade** (verificada pelo Revisor): por tese, **existe** ao menos **1 jurisprudência** (STJ/TJSC; STF quando adequado) ou, na **impossibilidade**, **1 doutrina**.

- **Estilo**: checklists atendidos; citações longas em bloco; algarismos; neutralidade; sem advérbios de ênfase.

- **Anti-plágio**: ausência de paralelismo excessivo com peças; **reelaboração analítica**.

- **Dispositivo**: fórmula canônica; coerente com a fundamentação.

- **Ementa**: fiel ao Voto; formato estrito; **zero inovação**.

**Falhas comuns e respostas do sistema:**

- **OCR ruim**: Saneamento aponta páginas e impacto; sugerir reenvio.

- **Prova controvertida**: Analista explicita tensões na tese e propõe trilha argumentativa prudente; Redator modula tom; Revisor testa robustez e citações.

- **Risco de excesso de linguagem (Júri)**: Analista aciona **Modo Júri**; Redator respeita; Revisor bloqueia desvios.

- **Ausência de autoridade**: Revisor classifica como “oportunidade de fortalecimento” (ou “crítica” se a tese depender essencialmente de autoridade) e indica fonte adequada.

- **Dosimetria fora de foco**: só aparece quando houver insurgência; se surgir resquício templético, Revisor suprime.

---

## 6) Operação prática (sem programação)

- **Sem modelos externos**: todos os **esqueletos** (Saneamento, Esboço, Blueprint, Handoff, Relatório Revisor, Reescrita, Ementa) **estarão embutidos nos respectivos prompts**.

- **Repositório do Redator**: **1–2 documentos** com **jurisprudência, doutrina e exemplos de tópicos/estilo** — **e nada mais**.

- **Memória do Gabinete**: documento anexado ao **[D] Maestro** para instaurar invariantes de estilo/formato e exemplos mínimos.

---

## 7) Encadeamento resumido (para uso diário)

1. **Maestro** (System + Memória) → “OK — Maestro carregado.”

2. **Analista** (System) → “OK — Analista pronto.”

3. **Kickoff + anexos** → Analista: “Recebido. Iniciando Etapa A: Saneamento → Esboço.”

4. **Diálogo Estratégico** → **Blueprint + Handoff**.

5. **Redator** (Claude) → **Voto** (sem ementa).

6. **Revisor** (Gemini) → **Relatório** → **Reescrita** (negritos + espaçamento).

7. **Ementa** (Gemini) → **Ementa final**.

---
