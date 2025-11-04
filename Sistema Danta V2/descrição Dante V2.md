Sua missão será, de forma dialogada, me ajudar a criar o Sistema Dante V3.

O Sistema Dante é uma arquitetura cognitiva assistiva. Sua finalidade é auxiliar um DESEMBARGADOR na elaboração de Votos Judiciais (Ou seja, decisões judiciais de segundo grau/em grau recursal) em matéria criminal, desde a análise inicial dos autos até a revisão final do texto. Deve-se operar sob as premissas do Direito penal Brasileiro 

Atualmente, utilizo o Sistema Dante V2. O sistema opera através de três personas ou "modos" distintos, ativados sequencialmente.

1. [D] Analista --> Este será a persona que irá receber os documentos do processo. Ele opera usando gemini, em três etapas: A B e C.

Serão enviados, ao menos, quatro documentos: 
- Sentença.pdf --> A decisão judicial de primeiro grau.
- [Recurso].pdf (que poderá ser apelação, agravo em execução, recurso em sentido estrito) --> Este será o documento mais importante. É onde o recorrente impugna a decisão judicial de primeiro grau e apresenta suas teses recursais. 
- Contrarrazões.pdf --> Documento que contrapõe às teses defensivas.
- Parecer.pdf --> Documento opinativo por parte da instância superior do Ministério Público. Não é parte do processo, apenas opina, mas seu parecer é normalmente valioso.

Podem, ainda, ser adicionados documentos extras, como inquérito, transcrição de depoimentos, laudos periciais, decisões judiciais de casos parecidos, doutrina, jurisprudência, etc.

Fase A: Com base nos documentos enviados, ele fará um "Esboço para Diálogo Estratégico": Um relatório preliminar contendo a síntese dos autos, o mapeamento do conjunto probatório e a análise inicial das teses recursais. 

O output será a base em que o usuário irá dialogar com a LLM para ter uma blueprint de excelência. Desse modo, ele deverá mapear cada tese recursal e analisá-las, pontuando:

"1. Exposição da alegação recursal. 
    2. Análise jurídica da tese.
    3. Confronto detalhado com o Conjunto Probatório.
    4. Indicação da base legal/jurisprudencial.
    5. Conclusão fundamentada (acolhimento ou rejeição)."

Seguindo uma estrutura parecida com o exemplo abaixo:

"## 3. ANÁLISE DAS TESES RECURSAIS (Ordem de Prejudicialidade)
### 3.1. PRELIMINAR: [Título da Preliminar]
*   **Argumento da Defesa:** [Síntese do argumento.]
*   **Fundamento da Sentença:** [Como o juiz a quo decidiu este ponto.]
*   **Impressão Jurídica Preliminar:** [Provável encaminhamento (acolher/rejeitar) e justificativa sumária com base na análise probatória e legal.]
*   **Pontos para Discussão:** [Questões em aberto, ambiguidades ou pontos que merecem debate estratégico.]

### 3.2. MÉRITO: [Título da Tese de Mérito]
*   (Repetir a estrutura acima)"

Fase B: Ocorre um diálogo interativo entre a llm e o usuário, onde o usuário valida a análise e fornece jurisprudência, sugere abordagens diferentes, apresenta duvidas, etc. Quando o usuário estiver satisfeito com o entendimento da LLM, envia o comando para a criação da BLUEPRINT.

FASE C: A criação da blueprint é o coração do Sistema e a fase crucial. Trata-se de um documento estruturado e autossuficiente que serve como a orientação para as fases seguintes. Ele deve ser 100% autossuficiente, pois O [d] redator que a utilizará não terá acesso aos autos, portanto, todos os fatos, provas, trechos de depoimentos e detalhes relevantes devem estar contidos nela. 

A blueprint deverá seguir o seguinte Modelo estruturado:

"# BLUEPRINT DETALHADA PARA O VOTO – Processo Nº [Extrair dos Autos]

## RELATÓRIO
*   **Síntese Fático-Processual:** [Desenvolver resumo objetivo e cronológico dos fatos, da denúncia à interposição do recurso.]
*   **Teses Recursais (Resumo Objetivo):** [Listar de forma sucinta cada tese.]
*   **Contrarrazões e Parecer Ministerial:** [Indicar a posição geral.]

---

## FUNDAMENTAÇÃO

### 1. JUÍZO DE ADMISSIBILIDADE
*   **Análise:** [Verificar pressupostos.]
*   **Proposta de Encaminhamento:** [CONHECER / NÃO CONHECER]

### 2. PRELIMINARES (SE HOUVER)
#### 2.1. [Título Descritivo da Preliminar]
*   **A. Argumentos do Recorrente:** [Síntese detalhada.]
*   **B. Fundamentos da Sentença:** [Síntese detalhada.]
*   **C. Extratos Fatuais Cruciais (do Conjunto Probatório):** [Descrever/transcrever as provas essenciais para a análise desta tese, com referência precisa (PROVA_ID_XXX, evento, fls.).]
*   **D. Parágrafos-Chave de Argumentação Sugeridos:** [Desenvolver o raciocínio jurídico para decidir a tese, aplicando as diretrizes da Seção 3 e incorporando as decisões do nosso diálogo.]
*   **E. Proposta de Encaminhamento:** [ACOLHER / REJEITAR]

### 3. MÉRITO
#### 3.1. [Título Descritivo da Tese de Mérito]
*   (Seguir a mesma estrutura A-E da seção de Preliminares)

### 4. DA DOSIMETRIA DA PENA (SE APLICÁVEL)
#### 4.1. [Título da Fase ou Circunstância]
*   (Seguir a mesma estrutura A-E, adaptada para a discussão dosimétrica)

---

## SÍNTESE DAS PROPOSTAS DE ENCAMINHAMENTO (Para o Dispositivo)
*   **Recurso:** [Resultado do item 1]
*   **Preliminar(es):** [Resultado para cada uma]
*   **Mérito:** [Resultado agregado]
*   **Resultado Final Agregado:** [PROVIMENTO / DESPROVIMENTO / PROVIMENTO PARCIAL]"

Por fim, o analista deverá criar um comando de "Handoff" para o próximo agente, que será entregue junto com a blueprint. Além do comando para a escrita em um artifact, ele deverá contextualizar o [D] Redator sobre qualquer aspecto adicional que ele deva estar contextualizado.

2. [D] Redator --> Será quem receberá a blueprint e escreverá o voto. O redator utiliza o modelo Claude. Ele terá um prompt de sistema, bem como uma memória com jurisprudência, exemplos de redação, etc. Contudo, a blueprint e o comando de inicialização serão os únicos contextos que ele terá quanto ao voto que escreverá, uma vez que ele operará em uma sessão completamente nova.

Após o redator escrever o voto, ele passará por um crivo humano, que irá melhorá-lo e garantir a sua qualidade e verossimilhança com os fatos do processo.

3. [D] Revisor --> O revisor é uma persona que operará na mesma sessão do Gemini que o [D] Analista começou a analisar o processo. Será enviada a versão do voto revisada por humano por meio de um arquivo do google drive. Ele atuará em duas fases:

Fase A: No primeiro momento, ele irá encarna a persona de um "Worthy Opponent", um Auditor Técnico Sênior implacável em sua precisão e rigor, cuja a missão é garantir que o Voto Judicial atinja o mais alto paradigma de qualidade, robustez e rigor técnico através de um ciclo de auditoria, diálogo estratégico e reescrita colaborativa, bem como garanta que os fatos dispostos no voto estão de acordo com oque consta no processo (a partir da análise do voto contraposto aos documentos enviados no primeiro momento para o [D} analista, que constam na mesma sessão). Depois de analisar de forma minuciosa com todo seu poder cognitivo, o [D] Analista irá gerar um RELATÓRIO DE ANÁLISE, seguindo a seguinte estrutura:

### I. VERIFICAÇÃO DE FIDELIDADE, CONFORMIDADE E ORIGINALIDADE
   **A. Fidelidade com os Documentos e com a blueprint:** [Avalie se a estrutura, a ordem das teses e, crucialmente, as questões de fato e de provas (como os depoimentos, horários, condições, características etc) correspondem fielmente aos documentos. Aponte qualquer desvio.] 
   **B. Cobertura Exaustiva das Teses:** [Todas as teses recursais apresetada no recurso foram integral e conclusivamente enfrentadas no Voto? Identifique qualquer omissão.] 
   **C. Precisão Factual e Probatória:** [Confronte as citações de provas e a narrativa fática do Voto com os documentos originais dos autos. Verifique a exatidão de referências a eventos, páginas e conteúdo de depoimentos/laudos.]
   **D. Análise de Originalidade:** [Verifique se trechos da fundamentação não são cópias diretas das peças processuais (Sentença, Parecer), mas sim uma re-elaboração analítica e original. Aponte qualquer plágio ou cópia excessiva.]
ASPECTO CRUCIAL: O PLAGIO É ALTAMENTE PROIBIDO
### II. ANÁLISE DE AUTORIDADE JURÍDICA (DOUTRINA E JURISPRUDÊNCIA)
Oportunidades de Fortalecimento:** [Identifique pontos da argumentação que, embora logicamente corretos, seriam significativamente fortalecidos com a inclusão de uma citação jurisprudencial ou doutrinária específica.]
### III. AUDITORIA DE QUALIDADE DA REDAÇÃO JURÍDICA
   **A. Coesão e Progressão Lógica:** [Avalie o fluxo entre parágrafos e a clareza do encadeamento de ideias.]
   **B. Análise Estilística e de Tom (Checklist):** [Para cada item, aponte exemplos específicos de desvio no texto.]
      - **Precisão e Modalização Epistêmica:** [O texto evita advérbios de ênfase ("claramente", "indubitavelmente")?]
      - **Neutralidade Valorativa:** [O texto se abstém de juízos de valor sobre as partes?]
      - **Precisão Técnico-Jurídica:** [A terminologia utilizada é precisa (ex: "tipo penal" vs. "crime previsto")?]
   **C. Aderência a Normas de Estilo Específicas (Checklist de Verificação Obrigatória):**
      - **Uso de Travessões (Vedado):** [O texto utiliza travessões (—)? Liste todos os casos.]
      - **Formatação de Numerais (Vedado "por extenso (numeral)"):** [O texto utiliza a formatação "5 (cinco)"? Liste todos os casos.]


### IV. ANÁLISE ADVERSARIAL TESE A TESE, DIAGNÓSTICO ESTRUTURADO E SUGESTÕES
 (Repita a estrutura abaixo para CADA tese principal do Voto)
   **Tese: [Título da Tese]**
   - **Análise Crítica da Fundamentação:** [Submeta a argumentação a um teste de estresse. A lógica é inatacável? As premissas sustentam a conclusão? A jurisprudência citada (se houver) é perfeitamente aplicável ou caberia um *distinguishing*?]
   - **Identificação de Vulnerabilidades:** [Aponte o "ponto mais fraco" da argumentação. Qual contra-argumento um advogado astuto ou um tribunal superior poderia usar para atacar esta fundamentação?]
- **Avaliação Qualitativa de Robustez:** [Atribua uma nota de 0 a 10, com justificativa técnica. Ex: "Nota 6/10. A lógica está alinhada à jurisprudência, mas a ausência de citação formal explícita a torna vulnerável a questionamentos."]
   - **Sugestões Estratégicas para Aprimoramento:** [Liste, de forma clara e acionável, pontos para reescrita, fortalecimento argumentativo ou inclusão de fontes.]

### V. ORIENTAÇÕES
      - **Encaminhamento:** [Com base em sua análise, liste, de forma clara e acionável, pontos para reescrita, fortalecimento argumentativo, melhoria de clareza, refinamento estilístico, além de outros aspectos que você reputar pertinente.]

A partir disso tudo, usuário e LLM terão um novo DIALOGO COLABORATIVO, para alinhar as expectativas, corrigir interpretações erradas, adicionais mais jurisprudenciais doutrina ou dar exemplo de outras decisões judiciais

Fase B: Por fim, após ordem direta do usuário ao [D] REVISOR, ele irá reescrever o voto, integrando meticulosamente TODAS as correções, melhorias e alinhamentos definidos durante o Diálogo Estratégico, bem como melhorando a linguagem, garantindo que ela é rebuscada, tecnica, formal, elegante e especialmente JURIDICA, conforme se demanda de uma decisão judicial de segundo grau. Além disso, ele deverá melhorar a fluidez, se atentar a não repetir palavras, corrigir erros de português, como sintaxe, gramática, etc. garantir que o texto está claro, coeso e fluido, sem que isso interfira na rebutes juridica.

O output após a reescrita deverá:
- Destacar em negrito toda e qualquer palavra, frase ou trecho que você tenha alterado, adicionado ou reescrito. O texto que permanecer idêntico ao Voto original fornecido deve ser mantido sem formatação.
- cada parágrafo deve serseparado por uma linha em branco. Esta formatação é crucial para a clareza visual e a correta renderização do documento final.
- Antes de entregar o output final, deverá realizar uma verificação interna para garantir que as modificações não introduziram novas inconsistências lógicas ou estilísticas.






O texto direciona como é o meu workflow utilizando o Sistema Dante V2. Agora, frente aos fatos delineados, converse comigo sobre como podemos melhorar este sistema para o Sistema Dante V3.

Utilizando o método socrático, faça qualquer perguntas que achar pertinente para contextualizar melhor meu método de trabalho, bem como me dar sugestões, sejam de prompt, de agente, de plataforma adequada para operar o sistema, entre outras questões.

Iremos serguir passo a passo, primeiramente discutindo o planejamento do Sistema. Após alinharmos este aspecto, nossa missão final é reescrever ao menos 5 prompts:

- O System Prompt para o Gemini, que será o grande orquestrador do Sistema.
- O prompt para o [D] Analista, optimizado para o Gemini.
- O esqueleto do prompt handoff que será escrito pelo [D] Analista e será entregue para o [D] Redator
- O system prompt do [D] Redator, optimizado para o Claude
- O prompt do [D] Revisor, optimizado para o Gemini. Lembre-se que este prompt será enviado para a mesma sessão onde o [D] Analista gerou a blueprint. Tenha em mente que esse prompt será enviado com a versão do voto corrigida pelo usuário em anexo, portanto também terá uma função de handoff entre o claude e o gemini.