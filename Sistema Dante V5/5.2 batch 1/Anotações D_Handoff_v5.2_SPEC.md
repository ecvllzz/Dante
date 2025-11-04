# [D] HANDOFF v5.2 — Sistema Dante Kickoff Specification

**Versão:** 5.2.0  
**Data:** 2025-10-21  
**Tipo:** Especificação técnica (XML Schema + Guidelines)  
**Changelog v5.2:**
- ✅ **CRÍTICO:** Adicionado campo `<estrutura_esperada>` para sinalizar Redator
- ✅ Mantida economia inteligente (campos opcionais)
- ✅ Refinados exemplos (simples, médio, complexo, Modo Júri)

---

## [PROPÓSITO DO HANDOFF]

O **Handoff** é o artefato de transição entre **Analista** (Gemini) e **Redator** (Claude). Ele contém:

1. **Contexto processual** mínimo mas suficiente
2. **Provas disponíveis** com IDs
3. **Teses estruturadas** com conclusões
4. **Ratio decidendi** alinhada
5. **Estrutura esperada** para o voto (NOVO em v5.2)
6. **Dispositivo canônico** (imutável)
7. **Orientações** de estilo, tom, peculiaridades

**Design principle:** Economia de tokens COM contexto suficiente.

---

## [CAMPOS OBRIGATÓRIOS vs. OPCIONAIS]

### Obrigatórios (Sempre Presentes)

- `<processo>`: número, órgão, vara
- `<tipo_peca>`: acórdão, sentença, voto, etc.
- `<estrutura_esperada>`: **NOVO v5.2** — sinaliza estrutura hierárquica
- `<fundamentos>`: contexto, provas, teses, ratio
- `<escopo>`: objetivo, estilo, extensão
- `<dispositivo_canonico>`: texto exato, imutável
- `<nao_fazer>`: políticas críticas

### Opcionais (Apenas Quando Necessário)

- `<banner_modo_juri>`: se crime doloso contra a vida
- `<peculiaridades>`: se caso tem aspectos atípicos
- `<sensibilidades>`: se requer cuidados especiais
- `<anexos>`: se há documentos adicionais

**Trade-off:**
- Caso simples: ~600 tokens (-78% vs. v4.1)
- Caso médio: ~1100 tokens (-60% vs. v4.1)
- Caso complexo: ~1800 tokens (-35% vs. v4.1, MAS contexto completo)

---

## [CAMPO NOVO: <estrutura_esperada>]

**Objetivo:** Sinalizar ao Redator a estrutura hierárquica esperada do voto.

**Formato:**

```xml
<estrutura_esperada>
  <tem_preliminares>true|false</tem_preliminares>
  <tem_dosimetria>true|false</tem_dosimetria>
  <numeracao>hierarquica|flat</numeracao>
  <secoes_merito>
    <secao>2.1. [Título da primeira tese]</secao>
    <secao>2.2. [Título da segunda tese]</secao>
    <!-- ... -->
  </secoes_merito>
</estrutura_esperada>
```

**Exemplos:**

### Exemplo 1: Apelação Simples (Sem Preliminares, Sem Dosimetria)

```xml
<estrutura_esperada>
  <tem_preliminares>false</tem_preliminares>
  <tem_dosimetria>false</tem_dosimetria>
  <numeracao>hierarquica</numeracao>
  <secoes_merito>
    <secao>2.1. Insuficiência probatória</secao>
  </secoes_merito>
</estrutura_esperada>
```

**Estrutura esperada do voto:**
```
I. RELATÓRIO

II. VOTO
   2. MÉRITO
      2.1. Insuficiência probatória

III. DISPOSITIVO
```

---

### Exemplo 2: Apelação com Preliminares e Mérito

```xml
<estrutura_esperada>
  <tem_preliminares>true</tem_preliminares>
  <tem_dosimetria>false</tem_dosimetria>
  <numeracao>hierarquica</numeracao>
  <secoes_merito>
    <secao>2.1. Tese de absolvição</secao>
    <secao>2.2. Tese subsidiária de desclassificação</secao>
  </secoes_merito>
</estrutura_esperada>
```

**Estrutura esperada do voto:**
```
I. RELATÓRIO

II. VOTO
   1. PRELIMINARES
      1.1. Nulidade por cerceamento de defesa
   
   2. MÉRITO
      2.1. Tese de absolvição
      2.2. Tese subsidiária de desclassificação

III. DISPOSITIVO
```

---

### Exemplo 3: Apelação com Dosimetria

```xml
<estrutura_esperada>
  <tem_preliminares>false</tem_preliminares>
  <tem_dosimetria>true</tem_dosimetria>
  <numeracao>hierarquica</numeracao>
  <secoes_merito>
    <secao>2.1. Condenação mantida</secao>
  </secoes_merito>
</estrutura_esperada>
```

**Estrutura esperada do voto:**
```
I. RELATÓRIO

II. VOTO
   2. MÉRITO
      2.1. Condenação mantida
   
   3. DOSIMETRIA
      3.1. Primeira fase (pena-base)
      3.2. Segunda fase (agravantes/atenuantes)
      3.3. Terceira fase (causas de aumento/diminuição)

III. DISPOSITIVO
```

---

### Exemplo 4: Caso Complexo (Preliminares + Mérito + Dosimetria)

```xml
<estrutura_esperada>
  <tem_preliminares>true</tem_preliminares>
  <tem_dosimetria>true</tem_dosimetria>
  <numeracao>hierarquica</numeracao>
  <secoes_merito>
    <secao>2.1. Tese de absolvição</secao>
    <secao>2.2. Tese de desclassificação</secao>
    <secao>2.3. Tese de prescrição</secao>
  </secoes_merito>
</estrutura_esperada>
```

**Estrutura esperada do voto:**
```
I. RELATÓRIO

II. VOTO
   1. PRELIMINARES
      1.1. Nulidade processual
      1.2. Incompetência do juízo
   
   2. MÉRITO
      2.1. Tese de absolvição
      2.2. Tese de desclassificação
      2.3. Tese de prescrição
   
   3. DOSIMETRIA
      3.1. Primeira fase
      3.2. Segunda fase
      3.3. Terceira fase

III. DISPOSITIVO
```

---

## [XML SCHEMA DEFINITION (XSD)]

```xsd
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <xs:element name="kickoff_redator">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="processo" type="ProcessoType"/>
        <xs:element name="tipo_peca" type="xs:string"/>
        <xs:element name="banner_modo_juri" type="BannerModoJuriType" minOccurs="0"/>
        <xs:element name="estrutura_esperada" type="EstruturaEsperadaType"/>
        <xs:element name="fundamentos" type="FundamentosType"/>
        <xs:element name="escopo" type="EscopoType"/>
        <xs:element name="anexos" type="AnexosType" minOccurs="0"/>
        <xs:element name="dispositivo_canonico" type="xs:string"/>
        <xs:element name="nao_fazer" type="NaoFazerType"/>
        <xs:element name="peculiaridades" type="PeculiaridadesType" minOccurs="0"/>
        <xs:element name="sensibilidades" type="SensibilidadesType" minOccurs="0"/>
      </xs:sequence>
      <xs:attribute name="version" type="xs:string" use="required"/>
    </xs:complexType>
  </xs:element>

  <xs:complexType name="ProcessoType">
    <xs:sequence>
      <xs:element name="numero" type="xs:string"/>
      <xs:element name="orgao" type="xs:string"/>
      <xs:element name="vara" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="BannerModoJuriType">
    <xs:sequence>
      <xs:element name="justificativa" type="xs:string"/>
      <xs:element name="orientacoes">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:sequence>
    <xs:attribute name="enabled" type="xs:boolean" use="required"/>
  </xs:complexType>

  <xs:complexType name="EstruturaEsperadaType">
    <xs:sequence>
      <xs:element name="tem_preliminares" type="xs:boolean"/>
      <xs:element name="tem_dosimetria" type="xs:boolean"/>
      <xs:element name="numeracao" type="NumeracaoType"/>
      <xs:element name="secoes_merito">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="secao" type="xs:string" maxOccurs="unbounded"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:sequence>
  </xs:complexType>

  <xs:simpleType name="NumeracaoType">
    <xs:restriction base="xs:string">
      <xs:enumeration value="hierarquica"/>
      <xs:enumeration value="flat"/>
    </xs:restriction>
  </xs:simpleType>

  <xs:complexType name="FundamentosType">
    <xs:sequence>
      <xs:element name="contexto_processual" type="xs:string"/>
      <xs:element name="provas_disponiveis">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="prova" maxOccurs="unbounded">
              <xs:complexType>
                <xs:simpleContent>
                  <xs:extension base="xs:string">
                    <xs:attribute name="id" type="xs:string" use="required"/>
                  </xs:extension>
                </xs:simpleContent>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
      <xs:element name="teses">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="tese" type="TeseType" maxOccurs="unbounded"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
      <xs:element name="ratio_decidendi" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="TeseType">
    <xs:sequence>
      <xs:element name="titulo" type="xs:string"/>
      <xs:element name="fundamento_legal" type="xs:string"/>
      <xs:element name="argumentos_defesa">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
      <xs:element name="contraargumentos_acusacao">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
      <xs:element name="jurisprudencia" minOccurs="0">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="precedente" maxOccurs="unbounded">
              <xs:complexType>
                <xs:simpleContent>
                  <xs:extension base="xs:string">
                    <xs:attribute name="tribunal" type="xs:string" use="required"/>
                    <xs:attribute name="numero" type="xs:string" use="required"/>
                  </xs:extension>
                </xs:simpleContent>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
      <xs:element name="fundamentacao_conclusao" type="xs:string"/>
    </xs:sequence>
    <xs:attribute name="id" type="xs:string" use="required"/>
    <xs:attribute name="conclusao" type="ConclusaoType" use="required"/>
  </xs:complexType>

  <xs:simpleType name="ConclusaoType">
    <xs:restriction base="xs:string">
      <xs:enumeration value="IMPROVÁVEL"/>
      <xs:enumeration value="POSSÍVEL"/>
      <xs:enumeration value="PROVÁVEL"/>
    </xs:restriction>
  </xs:simpleType>

  <xs:complexType name="EscopoType">
    <xs:sequence>
      <xs:element name="objetivo_redacao" type="xs:string"/>
      <xs:element name="estilo" type="xs:string"/>
      <xs:element name="extensao_estimada" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="AnexosType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="NaoFazerType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="PeculiaridadesType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="SensibilidadesType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

</xs:schema>
```

---

## [EXEMPLO COMPLETO: CASO SIMPLES]

**Contexto:** Furto simples, sem preliminares, sem dosimetria.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kickoff_redator version="5.2">
  <processo>
    <numero>0005678-90.2024.8.00.0000</numero>
    <orgao>Tribunal de Justiça do Estado X</orgao>
    <vara>1ª Câmara Criminal</vara>
  </processo>
  
  <tipo_peca>acórdão de apelação criminal</tipo_peca>
  
  <estrutura_esperada>
    <tem_preliminares>false</tem_preliminares>
    <tem_dosimetria>false</tem_dosimetria>
    <numeracao>hierarquica</numeracao>
    <secoes_merito>
      <secao>2.1. Insuficiência probatória</secao>
    </secoes_merito>
  </estrutura_esperada>
  
  <fundamentos>
    <contexto_processual>
      Apelação criminal contra sentença condenatória por furto simples (art. 155, CP). Defesa alega insuficiência probatória.
    </contexto_processual>
    
    <provas_disponiveis>
      <prova id="P01">Sentença condenatória</prova>
      <prova id="P02">Depoimento da vítima</prova>
      <prova id="P03">Objeto furtado apreendido</prova>
    </provas_disponiveis>
    
    <teses>
      <tese id="T1" conclusao="IMPROVÁVEL">
        <titulo>Insuficiência probatória</titulo>
        <fundamento_legal>Art. 386, VII, CPP</fundamento_legal>
        <argumentos_defesa>
          <item>P02 (vítima) não reconheceu réu diretamente</item>
          <item>P03 (objeto) foi encontrado, mas sem nexo com réu</item>
        </argumentos_defesa>
        <contraargumentos_acusacao>
          <item>P02 identificou réu por características físicas</item>
          <item>P03 foi apreendido na posse do réu</item>
          <item>Conjunto probatório é robusto</item>
        </contraargumentos_acusacao>
        <fundamentacao_conclusao>
          Provas P02 e P03 são suficientes para condenação. Tese deve ser rejeitada.
        </fundamentacao_conclusao>
      </tese>
    </teses>
    
    <ratio_decidendi>
      Manter condenação. Conjunto probatório é robusto e suficiente.
    </ratio_decidendi>
  </fundamentos>
  
  <escopo>
    <objetivo_redacao>
      Redigir voto rejeitando tese de insuficiência probatória e mantendo condenação.
    </objetivo_redacao>
    <estilo>Tom jurídico formal, acessível</estilo>
    <extensao_estimada>1500-2000 palavras</extensao_estimada>
  </escopo>
  
  <dispositivo_canonico>
    Nego provimento ao recurso.
  </dispositivo_canonico>
  
  <nao_fazer>
    <item>Não produzir ementa</item>
    <item>Não copiar trechos da sentença</item>
    <item>Não alterar dispositivo canônico</item>
  </nao_fazer>
</kickoff_redator>
```

**Tokens:** ~600 tokens (-78% vs. v4.1)

---

## [EXEMPLO COMPLETO: CASO MÉDIO]

**Contexto:** Roubo, com preliminar de nulidade e mérito.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kickoff_redator version="5.2">
  <processo>
    <numero>0007890-12.2024.8.00.0000</numero>
    <orgao>Tribunal de Justiça do Estado Y</orgao>
    <vara>3ª Câmara Criminal</vara>
  </processo>
  
  <tipo_peca>acórdão de apelação criminal</tipo_peca>
  
  <estrutura_esperada>
    <tem_preliminares>true</tem_preliminares>
    <tem_dosimetria>false</tem_dosimetria>
    <numeracao>hierarquica</numeracao>
    <secoes_merito>
      <secao>2.1. Autoria e materialidade</secao>
    </secoes_merito>
  </estrutura_esperada>
  
  <fundamentos>
    <contexto_processual>
      Apelação criminal contra sentença condenatória por roubo majorado (art. 157, §2º, I e II, CP). Defesa alega nulidade por cerceamento de defesa e, no mérito, insuficiência probatória.
    </contexto_processual>
    
    <provas_disponiveis>
      <prova id="P01">Sentença condenatória</prova>
      <prova id="P02">Depoimento da vítima (reconhecimento em audiência)</prova>
      <prova id="P03">Arma apreendida com o réu</prova>
      <prova id="P04">Laudo pericial da arma</prova>
    </provas_disponiveis>
    
    <teses>
      <tese id="T1" conclusao="IMPROVÁVEL">
        <titulo>Nulidade por cerceamento de defesa</titulo>
        <fundamento_legal>Art. 564, III, CPP</fundamento_legal>
        <argumentos_defesa>
          <item>Pedido de oitiva de testemunha foi indeferido pelo juiz</item>
          <item>Testemunha poderia comprovar álibi</item>
        </argumentos_defesa>
        <contraargumentos_acusacao>
          <item>Pedido foi tardio (após encerramento da instrução)</item>
          <item>Testemunha não foi arrolada tempestivamente</item>
          <item>Não há prejuízo demonstrado</item>
        </contraargumentos_acusacao>
        <fundamentacao_conclusao>
          Pedido tardio e sem demonstração de prejuízo. Tese deve ser rejeitada.
        </fundamentacao_conclusao>
      </tese>
      
      <tese id="T2" conclusao="IMPROVÁVEL">
        <titulo>Insuficiência probatória (autoria e materialidade)</titulo>
        <fundamento_legal>Art. 386, VII, CPP</fundamento_legal>
        <argumentos_defesa>
          <item>P02 (reconhecimento) foi único meio de prova de autoria</item>
          <item>Reconhecimento em audiência é frágil</item>
        </argumentos_defesa>
        <contraargumentos_acusacao>
          <item>P02 reconheceu réu com segurança e detalhes</item>
          <item>P03 e P04 comprovam que réu estava armado</item>
          <item>Conjunto probatório é robusto</item>
        </contraargumentos_acusacao>
        <fundamentacao_conclusao>
          P02 é prova robusta, corroborada por P03 e P04. Tese deve ser rejeitada.
        </fundamentacao_conclusao>
      </tese>
    </teses>
    
    <ratio_decidendi>
      Rejeitar preliminar de nulidade. No mérito, manter condenação por roubo majorado.
    </ratio_decidendi>
  </fundamentos>
  
  <escopo>
    <objetivo_redacao>
      Redigir voto rejeitando preliminar e mérito. Estrutura: Relatório + Preliminares (1.1) + Mérito (2.1) + Dispositivo.
    </objetivo_redacao>
    <estilo>Tom jurídico formal</estilo>
    <extensao_estimada>2500-3000 palavras</extensao_estimada>
  </escopo>
  
  <dispositivo_canonico>
    Rejeito a preliminar. Nego provimento ao recurso.
  </dispositivo_canonico>
  
  <nao_fazer>
    <item>Não produzir ementa</item>
    <item>Não copiar trechos da sentença</item>
    <item>Não alterar dispositivo canônico</item>
  </nao_fazer>
</kickoff_redator>
```

**Tokens:** ~1100 tokens (-60% vs. v4.1)

---

## [EXEMPLO COMPLETO: CASO COMPLEXO + MODO JÚRI]

**Contexto:** Homicídio doloso qualificado, com preliminar, mérito (2 teses) e dosimetria. Modo Júri ativo.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kickoff_redator version="5.2">
  <processo>
    <numero>0001234-56.2024.8.00.0000</numero>
    <orgao>Tribunal de Justiça do Estado Z</orgao>
    <vara>2ª Câmara Criminal</vara>
  </processo>
  
  <tipo_peca>acórdão de apelação criminal</tipo_peca>
  
  <banner_modo_juri enabled="true">
    <justificativa>Crime doloso contra a vida (homicídio qualificado)</justificativa>
    <orientacoes>
      <item>Usar linguagem de prelibação (indícios, aparenta, segundo acusação)</item>
      <item>Evitar afirmações categóricas sobre autoria/materialidade</item>
      <item>Ressalvar competência do júri para mérito</item>
    </orientacoes>
  </banner_modo_juri>
  
  <estrutura_esperada>
    <tem_preliminares>true</tem_preliminares>
    <tem_dosimetria>true</tem_dosimetria>
    <numeracao>hierarquica</numeracao>
    <secoes_merito>
      <secao>2.1. Tese de absolvição sumária</secao>
      <secao>2.2. Tese de desclassificação</secao>
    </secoes_merito>
  </estrutura_esperada>
  
  <fundamentos>
    <contexto_processual>
      Apelação criminal contra sentença de pronúncia por homicídio doloso qualificado (art. 121, §2º, I e IV, CP). Defesa alega preliminar de incompetência do juízo, absolvição sumária por insuficiência probatória e, subsidiariamente, desclassificação para homicídio simples. Há também pedido de redução da pena fixada em dosimetria provisória.
    </contexto_processual>
    
    <provas_disponiveis>
      <prova id="P01">Sentença de pronúncia</prova>
      <prova id="P02">Depoimento de testemunha Maria Silva (contexto pré-crime)</prova>
      <prova id="P03">Depoimento de testemunha João Santos (réu com arma)</prova>
      <prova id="P04">Laudo necroscópico (causa mortis)</prova>
      <prova id="P05">Laudo de local (trajetória balística)</prova>
    </provas_disponiveis>
    
    <teses>
      <tese id="T1" conclusao="IMPROVÁVEL">
        <titulo>Preliminar de incompetência do juízo</titulo>
        <fundamento_legal>Art. 564, I, CPP</fundamento_legal>
        <argumentos_defesa>
          <item>Crime ocorreu em comarca diversa</item>
          <item>Competência do juízo de origem deveria ser reconhecida</item>
        </argumentos_defesa>
        <contraargumentos_acusacao>
          <item>Competência foi fixada corretamente (local do resultado)</item>
          <item>Não há prejuízo demonstrado</item>
        </contraargumentos_acusacao>
        <fundamentacao_conclusao>
          Competência fixada corretamente. Tese deve ser rejeitada.
        </fundamentacao_conclusao>
      </tese>
      
      <tese id="T2" conclusao="IMPROVÁVEL">
        <titulo>Absolvição sumária por insuficiência probatória</titulo>
        <fundamento_legal>Art. 415, CPP</fundamento_legal>
        <argumentos_defesa>
          <item>P02 e P03 não viram momento exato do disparo</item>
          <item>Reconhecimento de P03 é frágil</item>
          <item>Ausência de prova direta de autoria</item>
        </argumentos_defesa>
        <contraargumentos_acusacao>
          <item>P03 viu réu com arma antes do crime</item>
          <item>P05 confirma disparo de arma compatível</item>
          <item>Pronúncia exige apenas indícios (in dubio pro societate)</item>
        </contraargumentos_acusacao>
        <jurisprudencia>
          <precedente tribunal="STJ" numero="HC 123456">
            Pronúncia exige apenas indícios, não prova plena. In dubio pro societate prevalece.
          </precedente>
        </jurisprudencia>
        <fundamentacao_conclusao>
          Conjunto probatório indiciário (P03 + P05) é suficiente para pronúncia segundo precedente STJ HC 123456. Tese de absolvição sumária deve ser rejeitada.
        </fundamentacao_conclusao>
      </tese>
      
      <tese id="T3" conclusao="PROVÁVEL">
        <titulo>Desclassificação para homicídio simples</titulo>
        <fundamento_legal>Art. 121, caput, CP</fundamento_legal>
        <argumentos_defesa>
          <item>Qualificadora I (motivo fútil): P02 não demonstra futilidade</item>
          <item>Qualificadora IV (recurso que dificultou defesa): P05 não comprova traição</item>
        </argumentos_defesa>
        <contraargumentos_acusacao>
          <item>P02 menciona discussão por motivo banal (contexto de futilidade)</item>
          <item>P05 indica disparo pelas costas (recurso que dificultou defesa)</item>
        </contraargumentos_acusacao>
        <jurisprudencia>
          <precedente tribunal="TJ-SP" numero="APL 789">
            Qualificadora de motivo fútil exige demonstração clara e inequívoca. Na dúvida, in dubio pro reo.
          </precedente>
        </jurisprudencia>
        <fundamentacao_conclusao>
          Qualificadora I é frágil (P02 não sustenta futilidade clara). Precedente TJ-SP APL 789 favorece desclassificação. Tese deve ser parcialmente acolhida.
        </fundamentacao_conclusao>
      </tese>
      
      <tese id="T4" conclusao="PROVÁVEL">
        <titulo>Redução de pena na dosimetria</titulo>
        <fundamento_legal>Art. 59, CP</fundamento_legal>
        <argumentos_defesa>
          <item>Pena-base fixada no máximo sem justificativa adequada</item>
          <item>Circunstâncias judiciais são favoráveis (réu primário, bons antecedentes)</item>
        </argumentos_defesa>
        <contraargumentos_acusacao>
          <item>Crime grave justifica pena-base elevada</item>
          <item>Circunstâncias do crime são desfavoráveis</item>
        </contraargumentos_acusacao>
        <fundamentacao_conclusao>
          Circunstâncias judiciais são mistas. Pena-base pode ser reduzida para o mínimo legal. Tese deve ser parcialmente acolhida.
        </fundamentacao_conclusao>
      </tese>
    </teses>
    
    <ratio_decidendi>
      Rejeitar preliminar e absolvição sumária. Acolher parcialmente tese de desclassificação (afastar qualificadora I) e redução de pena na dosimetria.
    </ratio_decidendi>
  </fundamentos>
  
  <escopo>
    <objetivo_redacao>
      Redigir voto com estrutura completa: Preliminares (1.1) + Mérito (2.1 Absolvição, 2.2 Desclassificação) + Dosimetria (3.1, 3.2, 3.3) + Dispositivo.
    </objetivo_redacao>
    <estilo>Tom jurídico formal, linguagem de prelibação (Modo Júri ativo)</estilo>
    <extensao_estimada>4000-5000 palavras</extensao_estimada>
  </escopo>
  
  <anexos>
    <item>Blueprint completa (JSON)</item>
  </anexos>
  
  <dispositivo_canonico>
    Rejeito a preliminar. Nego provimento ao recurso quanto à absolvição sumária. Dou parcial provimento para desclassificar o crime de homicídio qualificado para homicídio simples, mantendo a pronúncia, e para reduzir a pena-base ao mínimo legal.
  </dispositivo_canonico>
  
  <nao_fazer>
    <item>Não produzir ementa</item>
    <item>Não copiar trechos da sentença ou acórdão</item>
    <item>Não alterar dispositivo canônico</item>
    <item>Não fazer afirmações categóricas sobre autoria/materialidade (Modo Júri)</item>
  </nao_fazer>
  
  <peculiaridades>
    <item>Modo Júri ativado (crime doloso contra a vida)</item>
    <item>Caso complexo com 4 teses distintas</item>
    <item>Dosimetria provisória em fase de pronúncia</item>
  </peculiaridades>
  
  <sensibilidades>
    <item>Evitar prejulgar mérito (competência do júri)</item>
    <item>Ponderar qualificadoras com cautela (in dubio pro reo em caso de dúvida)</item>
    <item>Dosimetria provisória: ajustar apenas se manifestamente ilegal</item>
  </sensibilidades>
</kickoff_redator>
```

**Tokens:** ~1800 tokens (-35% vs. v4.1, MAS contexto COMPLETO)

---

## [VALIDAÇÃO XML]

### Comando para validar Handoff:

```bash
xmllint --schema schemas/handoff_v5.2.xsd handoff.xml
```

**Output esperado:**
```
handoff.xml validates
```

**Se inválido:**
```
handoff.xml:15: parser error : Opening and ending tag mismatch
<tese id="T1">
             ^
```

**Solução:** Corrigir XML e re-validar.

---

## [TROUBLESHOOTING]

### Problema: Campo <estrutura_esperada> faltando

**Sintoma:** Redator gera estrutura "flat" sem hierarquia

**Diagnóstico:** Handoff v5.1 não tinha `<estrutura_esperada>`

**Solução:** Adicionar campo obrigatório:
```xml
<estrutura_esperada>
  <tem_preliminares>false</tem_preliminares>
  <tem_dosimetria>false</tem_dosimetria>
  <numeracao>hierarquica</numeracao>
  <secoes_merito>
    <secao>2.1. [Título]</secao>
  </secoes_merito>
</estrutura_esperada>
```

---

### Problema: Modo Júri não detectado

**Sintoma:** Banner ausente em caso de crime doloso contra a vida

**Diagnóstico:** Analista não ativou banner

**Solução:** Adicionar banner obrigatório:
```xml
<banner_modo_juri enabled="true">
  <justificativa>Crime doloso contra a vida</justificativa>
  <orientacoes>
    <item>Usar linguagem de prelibação</item>
  </orientacoes>
</banner_modo_juri>
```

---

### Problema: Handoff muito longo (>2000 tokens)

**Sintoma:** Caso simples com Handoff de 2500 tokens

**Diagnóstico:** Campos opcionais preenchidos desnecessariamente

**Solução:**
- Remover `<peculiaridades>` se caso é padrão
- Remover `<sensibilidades>` se não há cuidados especiais
- Remover `<anexos>` se não há documentos adicionais

---

## [MÉTRICAS DE QUALIDADE]

### Checklist de Validação (Auto-Verificação do Analista)

Antes de gerar Handoff:
- [ ] Todos os campos obrigatórios presentes?
- [ ] Campo `<estrutura_esperada>` preenchido corretamente?
- [ ] IDs de prova consistentes (P01, P02...)?
- [ ] Modo Júri ativado se crime doloso contra a vida?
- [ ] Dispositivo canônico copiado exatamente da Blueprint?
- [ ] XML válido (xmllint pass)?

---

## [VERSIONAMENTO]

**v5.2.0 (2025-10-21):**
- Campo `<estrutura_esperada>` adicionado (CRÍTICO)
- Exemplos refinados (simples, médio, complexo)
- XSD atualizado

**v5.1.0 (2025-10-19):**
- Campos opcionais
- Economia inteligente

**v5.0.0 (2025-10-15):**
- Primeira versão estruturada

---

**FIM DO DOCUMENTO**
