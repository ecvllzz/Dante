# [D] HANDOFF v5.1 — Sistema Dante Data Transfer Object

**Versão:** 5.1.0  
**Data:** 2025-10-19  
**Tipo:** Especificação Técnica (não é um agente)  
**Changelog v5.1:**
- ✅ Recuperado `<contexto_processual>`, `<peculiaridades>`, `<sensibilidades>` (sem inflar tokens)
- ✅ Estruturado `<foco_redacional>` com campos tipados (desafios, requisitos, estimativas)
- ✅ Refinado XML Schema (XSD) com validação rigorosa
- ✅ Adicionados exemplos de uso para diferentes níveis de complexidade
- ✅ Preparado para testes de regressão

---

## [DEFINIÇÃO]

O **Handoff** é o artefato de transferência de dados entre o **Analista** e o **Redator**. É um documento XML estruturado que contém:

1. **Dados processuais** (número, órgão, tipo de peça)
2. **Teses estruturadas** (fundamentos, contra-argumentos, elementos probatórios, jurisprudência, conclusão)
3. **Fundamentos jurídicos** (artigos, súmulas, princípios)
4. **Dispositivo canônico** (decisão final, não editável pelo Redator)
5. **Contexto processual** (informações de background)
6. **Peculiaridades** (aspectos atípicos do caso)
7. **Sensibilidades** (cuidados redacionais)
8. **Foco redacional estruturado** (desafios técnicos, requisitos, estimativas)
9. **Instruções de não fazer** (políticas Dante)
10. **Métricas** (tempo de geração, complexidade)

---

## [ECONOMIA DE TOKENS vs. CONTEXTO]

**Princípio de Design:**
- Handoff v5.1 mantém economia brutal (~80 linhas, -70% vs. v4.1)
- MAS recupera contexto essencial que v5.0 perdeu (contexto processual, peculiaridades, sensibilidades)
- **Estratégia:** Campos opcionais e preenchidos apenas quando necessário

**Comparação:**

| Campo | v4.1 | v5.0 | v5.1 | Justificativa v5.1 |
|-------|------|------|------|-------------------|
| Dados processuais | ✅ | ✅ | ✅ | Essencial |
| Teses estruturadas | ✅ | ✅ | ✅ | Essencial |
| Fundamentos jurídicos | ✅ | ✅ | ✅ | Essencial |
| Dispositivo | ✅ | ✅ | ✅ | Essencial (canônico) |
| Contexto processual | ✅ | ❌ | ✅ | **RECUPERADO** — crítico para casos complexos |
| Peculiaridades | ✅ | ❌ | ✅ | **RECUPERADO** — previne erros em casos atípicos |
| Sensibilidades | ✅ | ❌ | ✅ | **RECUPERADO** — cuidados redacionais |
| Foco redacional (texto livre) | ✅ | ✅ | ❌ | Substituído por campos tipados |
| Foco estruturado | ❌ | ❌ | ✅ | **NOVO** — desafios + requisitos + estimativas |

**Economia de tokens (estimada):**
- v4.1: ~2800 tokens
- v5.0: ~800 tokens (-71%)
- v5.1: ~1200 tokens (-57% vs. v4.1, mas +50% vs. v5.0)

**Decisão:** Aceitamos +50% tokens vs. v5.0 para recuperar contexto essencial e eliminar ambiguidade do foco.

---

## [XML SCHEMA (XSD) v5.1]

```xsd
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" 
           elementFormDefault="qualified" 
           attributeFormDefault="unqualified">

  <!-- ROOT ELEMENT -->
  <xs:element name="kickoff_redator">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="processo" type="ProcessoType"/>
        <xs:element name="tipo_peca" type="TipoPecaType"/>
        <xs:element name="prioridade" type="PrioridadeType"/>
        <xs:element name="banner_modo_juri" type="BannerModoJuriType"/>
        <xs:element name="teses" type="TesesType"/>
        <xs:element name="fundamentos_juridicos" type="FundamentosJuridicosType"/>
        <xs:element name="dispositivo" type="xs:string"/>
        <xs:element name="contexto_processual" type="ContextoProcessualType" minOccurs="0"/>
        <xs:element name="peculiaridades" type="PeculiaridadesType" minOccurs="0"/>
        <xs:element name="sensibilidades" type="SensibilidadesType" minOccurs="0"/>
        <xs:element name="foco_redacional" type="FocoRedacionalType"/>
        <xs:element name="dossie_referencia" type="DossieReferenciaType"/>
        <xs:element name="nao_fazer" type="NaoFazerType"/>
        <xs:element name="metrics" type="MetricsType" minOccurs="0"/>
      </xs:sequence>
      <xs:attribute name="version" type="xs:string" use="required" fixed="5.1.0"/>
    </xs:complexType>
  </xs:element>

  <!-- PROCESSO -->
  <xs:complexType name="ProcessoType">
    <xs:attribute name="numero" type="xs:string" use="required"/>
    <xs:attribute name="orgao" type="xs:string" use="required"/>
  </xs:complexType>

  <!-- TIPO DE PEÇA -->
  <xs:complexType name="TipoPecaType">
    <xs:attribute name="value" use="required">
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:enumeration value="voto_apelacao_criminal"/>
          <xs:enumeration value="voto_agravo_execucao"/>
          <xs:enumeration value="voto_habeas_corpus"/>
          <xs:enumeration value="voto_revisao_criminal"/>
          <xs:enumeration value="decisao_monocratica"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:complexType>

  <!-- PRIORIDADE -->
  <xs:complexType name="PrioridadeType">
    <xs:attribute name="value" use="required">
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:enumeration value="baixa"/>
          <xs:enumeration value="media"/>
          <xs:enumeration value="alta"/>
          <xs:enumeration value="urgente"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:complexType>

  <!-- BANNER MODO JÚRI -->
  <xs:complexType name="BannerModoJuriType">
    <xs:attribute name="enabled" type="xs:boolean" use="required"/>
  </xs:complexType>

  <!-- TESES -->
  <xs:complexType name="TesesType">
    <xs:sequence>
      <xs:element name="tese" type="TeseType" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="TeseType">
    <xs:sequence>
      <xs:element name="titulo" type="xs:string"/>
      <xs:element name="fundamento_recorrente" type="xs:string"/>
      <xs:element name="contra_argumento" type="xs:string" minOccurs="0"/>
      <xs:element name="elementos_probatorios" type="ElementosProbatoriosType"/>
      <xs:element name="jurisprudencia_aplicavel" type="JurisprudenciaAplicavelType" minOccurs="0"/>
      <xs:element name="conclusao" type="xs:string"/>
    </xs:sequence>
    <xs:attribute name="id" type="xs:positiveInteger" use="required"/>
    <xs:attribute name="status" use="required">
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:enumeration value="acolhida"/>
          <xs:enumeration value="acolhida_parcialmente"/>
          <xs:enumeration value="rejeitada"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:complexType>

  <xs:complexType name="ElementosProbatoriosType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="JurisprudenciaAplicavelType">
    <xs:sequence>
      <xs:element name="precedente" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <!-- FUNDAMENTOS JURÍDICOS -->
  <xs:complexType name="FundamentosJuridicosType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <!-- CONTEXTO PROCESSUAL (OPCIONAL) -->
  <xs:complexType name="ContextoProcessualType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <!-- PECULIARIDADES (OPCIONAL) -->
  <xs:complexType name="PeculiaridadesType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <!-- SENSIBILIDADES (OPCIONAL) -->
  <xs:complexType name="SensibilidadesType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <!-- FOCO REDACIONAL (ESTRUTURADO) -->
  <xs:complexType name="FocoRedacionalType">
    <xs:sequence>
      <xs:element name="desafios" type="DesafiosType"/>
      <xs:element name="requisitos_redacionais" type="RequisitosRedacionaisType"/>
      <xs:element name="estimativas" type="EstimativasType"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="DesafiosType">
    <xs:sequence>
      <xs:element name="desafio" type="DesafioType" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="DesafioType">
    <xs:sequence>
      <xs:element name="descricao" type="xs:string"/>
      <xs:element name="atencao_redacional" type="xs:string"/>
    </xs:sequence>
    <xs:attribute name="type" use="required">
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:enumeration value="probatorio"/>
          <xs:enumeration value="dosimetrico"/>
          <xs:enumeration value="processual"/>
          <xs:enumeration value="constitucional"/>
          <xs:enumeration value="jurisprudencial"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:complexType>

  <xs:complexType name="RequisitosRedacionaisType">
    <xs:sequence>
      <xs:element name="requisito" type="RequisitoType" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="RequisitoType">
    <xs:simpleContent>
      <xs:extension base="xs:string">
        <xs:attribute name="type" use="required">
          <xs:simpleType>
            <xs:restriction base="xs:string">
              <xs:enumeration value="estrutura"/>
              <xs:enumeration value="tom"/>
              <xs:enumeration value="extensao"/>
              <xs:enumeration value="enfase"/>
            </xs:restriction>
          </xs:simpleType>
        </xs:attribute>
        <xs:attribute name="value" type="xs:string" use="required"/>
      </xs:extension>
    </xs:simpleContent>
  </xs:complexType>

  <xs:complexType name="EstimativasType">
    <xs:sequence>
      <xs:element name="complexidade">
        <xs:simpleType>
          <xs:restriction base="xs:string">
            <xs:enumeration value="simples"/>
            <xs:enumeration value="media"/>
            <xs:enumeration value="complexa"/>
            <xs:enumeration value="muito_complexa"/>
          </xs:restriction>
        </xs:simpleType>
      </xs:element>
      <xs:element name="tempo_estimado_minutos" type="xs:positiveInteger"/>
      <xs:element name="nivel_tecnico">
        <xs:simpleType>
          <xs:restriction base="xs:string">
            <xs:enumeration value="baixo"/>
            <xs:enumeration value="medio"/>
            <xs:enumeration value="alto"/>
          </xs:restriction>
        </xs:simpleType>
      </xs:element>
    </xs:sequence>
  </xs:complexType>

  <!-- DOSSIÊ REFERÊNCIA -->
  <xs:complexType name="DossieReferenciaType">
    <xs:attribute name="version" type="xs:string" use="required"/>
    <xs:attribute name="url" type="xs:string"/>
  </xs:complexType>

  <!-- NÃO FAZER -->
  <xs:complexType name="NaoFazerType">
    <xs:sequence>
      <xs:element name="item" type="xs:string" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>

  <!-- MÉTRICAS -->
  <xs:complexType name="MetricsType">
    <xs:sequence>
      <xs:element name="blueprint_generation_time_ms" type="xs:positiveInteger"/>
      <xs:element name="handoff_generation_time_ms" type="xs:positiveInteger"/>
      <xs:element name="total_time_ms" type="xs:positiveInteger"/>
    </xs:sequence>
  </xs:complexType>

</xs:schema>
```

---

## [EXEMPLOS DE USO]

### Exemplo 1: Caso Simples (Mínimo de Contexto)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kickoff_redator version="5.1.0">
  
  <processo numero="12345-67.2024.8.26.0001" orgao="1ª Câmara Criminal do TJSP"/>
  <tipo_peca value="voto_apelacao_criminal"/>
  <prioridade value="baixa"/>
  
  <banner_modo_juri enabled="false"/>
  
  <teses>
    <tese id="1" status="acolhida">
      <titulo>Dosimetria — Redução de Pena</titulo>
      <fundamento_recorrente>
        Pena-base acima do mínimo sem fundamentação idônea.
      </fundamento_recorrente>
      <elementos_probatorios>
        <item>Sentença, fls. 50-55 (dosimetria)</item>
      </elementos_probatorios>
      <conclusao>
        Reduzir pena-base para o mínimo legal (4 anos).
      </conclusao>
    </tese>
  </teses>
  
  <fundamentos_juridicos>
    <item>Art. 155, CP — Furto</item>
    <item>Art. 59, CP — Circunstâncias judiciais</item>
  </fundamentos_juridicos>
  
  <dispositivo>
    DAR PROVIMENTO ao recurso para reduzir a pena para 4 (quatro) anos de reclusão.
  </dispositivo>
  
  <!-- Campos opcionais omitidos (contexto, peculiaridades, sensibilidades) -->
  
  <foco_redacional>
    <desafios>
      <desafio type="dosimetrico">
        <descricao>Sentença aumentou pena-base sem fundamentação clara</descricao>
        <atencao_redacional>
          Demonstrar ausência de fundamentação idônea para aumento
        </atencao_redacional>
      </desafio>
    </desafios>
    
    <requisitos_redacionais>
      <requisito type="estrutura" value="tripartida">
        Relatório + Voto + Dispositivo
      </requisito>
      <requisito type="extensao" value="curto">
        1-2 páginas
      </requisito>
    </requisitos_redacionais>
    
    <estimativas>
      <complexidade>simples</complexidade>
      <tempo_estimado_minutos>20</tempo_estimado_minutos>
      <nivel_tecnico>baixo</nivel_tecnico>
    </estimativas>
  </foco_redacional>
  
  <dossie_referencia version="5.1" url="[path]"/>
  
  <nao_fazer>
    <item>Não produzir ementa</item>
    <item>Não alterar dispositivo canônico</item>
  </nao_fazer>
  
</kickoff_redator>
```

**Economia:** ~600 tokens (~50% vs. v5.1 máximo)

---

### Exemplo 2: Caso Médio (Contexto e Peculiaridades)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kickoff_redator version="5.1.0">
  
  <processo numero="98765-43.2024.8.26.0001" orgao="2ª Câmara Criminal do TJSP"/>
  <tipo_peca value="voto_apelacao_criminal"/>
  <prioridade value="media"/>
  
  <banner_modo_juri enabled="false"/>
  
  <teses>
    <tese id="1" status="acolhida_parcialmente">
      <titulo>Dosimetria Excessiva</titulo>
      <fundamento_recorrente>
        Circunstâncias judiciais não justificam aumento de 1 ano.
      </fundamento_recorrente>
      <contra_argumento>
        Culpabilidade moderada e consequências graves justificam aumento (contrarrazões).
      </contra_argumento>
      <elementos_probatorios>
        <item>Sentença, fls. 205-208 (dosimetria)</item>
        <item>Laudo psicológico, fls. 120</item>
      </elementos_probatorios>
      <jurisprudencia_aplicavel>
        <precedente>STJ, HC 123.456 — fundamentação idônea para aumento na 1ª fase</precedente>
      </jurisprudencia_aplicavel>
      <conclusao>
        Reduzir pena de 6 para 5 anos sem prejuízo à proporcionalidade.
      </conclusao>
    </tese>
  </teses>
  
  <fundamentos_juridicos>
    <item>Art. 157, §2º, II, CP — Roubo qualificado</item>
    <item>Art. 59, CP — Circunstâncias judiciais</item>
  </fundamentos_juridicos>
  
  <dispositivo>
    DAR PARCIAL PROVIMENTO ao recurso para reduzir a pena de 6 (seis) anos para 5 (cinco) anos de reclusão.
  </dispositivo>
  
  <contexto_processual>
    <item>Réu primário e com bons antecedentes</item>
    <item>Contrarrazões sólidas do MP pela manutenção da dosimetria</item>
  </contexto_processual>
  
  <peculiaridades>
    <item>Laudo psicológico aponta culpabilidade "leve a moderada", não "moderada a grave"</item>
  </peculiaridades>
  
  <sensibilidades>
    <item>Caso com repercussão midiática local — cuidado ao reduzir pena</item>
  </sensibilidades>
  
  <foco_redacional>
    <desafios>
      <desafio type="dosimetrico">
        <descricao>Balancear jurisprudência do STJ vs. TJSP sobre aumento na 1ª fase</descricao>
        <atencao_redacional>
          Explicar redução sem invalidar fundamentação da sentença
        </atencao_redacional>
      </desafio>
    </desafios>
    
    <requisitos_redacionais>
      <requisito type="estrutura" value="tripartida">
        Relatório + Voto + Dispositivo
      </requisito>
      <requisito type="tom" value="tecnico_equilibrado">
        Tom técnico, reconhecendo fundamentos da sentença antes de reduzir
      </requisito>
      <requisito type="extensao" value="medio">
        3-4 páginas
      </requisito>
    </requisitos_redacionais>
    
    <estimativas>
      <complexidade>media</complexidade>
      <tempo_estimado_minutos>45</tempo_estimado_minutos>
      <nivel_tecnico>medio</nivel_tecnico>
    </estimativas>
  </foco_redacional>
  
  <dossie_referencia version="5.1" url="[path]"/>
  
  <nao_fazer>
    <item>Não produzir ementa</item>
    <item>Não copiar integralmente trechos da sentença</item>
    <item>Não alterar dispositivo canônico</item>
  </nao_fazer>
  
</kickoff_redator>
```

**Economia:** ~1100 tokens (~60% vs. v4.1)

---

### Exemplo 3: Caso Complexo + Modo Júri (Contexto Máximo)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kickoff_redator version="5.1.0">
  
  <processo numero="11111-22.2024.8.26.0001" orgao="3ª Câmara Criminal do TJSP"/>
  <tipo_peca value="voto_apelacao_criminal"/>
  <prioridade value="alta"/>
  
  <banner_modo_juri enabled="true"/>
  
  <teses>
    <tese id="1" status="acolhida">
      <titulo>Prova Insuficiente para Pronúncia</titulo>
      <fundamento_recorrente>
        Conjunto probatório insuficiente para submeter ao Júri.
      </fundamento_recorrente>
      <contra_argumento>
        Indícios convergem para autoria (contrarrazões do MP).
      </contra_argumento>
      <elementos_probatorios>
        <item>Depoimento testemunhal único (fls. 80-82)</item>
        <item>Laudo pericial inconclusivo (fls. 150-160)</item>
        <item>Ausência de prova técnica direta</item>
      </elementos_probatorios>
      <jurisprudencia_aplicavel>
        <precedente>STF, HC 345.678 — pronúncia exige justa causa probatória mínima</precedente>
        <precedente>TJSP, Apelação 1234567-89 — indícios frágeis não justificam pronúncia</precedente>
      </jurisprudencia_aplicavel>
      <conclusao>
        Conjunto probatório insuficiente. Impronúncia é medida adequada (juízo de admissibilidade).
      </conclusao>
    </tese>
    
    <tese id="2" status="rejeitada">
      <titulo>Nulidade — Ausência de Defesa Técnica</titulo>
      <fundamento_recorrente>
        Defensor não atuou efetivamente no interrogatório.
      </fundamento_recorrente>
      <contra_argumento>
        Ata do interrogatório registra presença e manifestação do defensor.
      </contra_argumento>
      <elementos_probatorios>
        <item>Ata de interrogatório (fls. 40-42)</item>
      </elementos_probatorios>
      <conclusao>
        Não há nulidade. Defensor estava presente e atuou.
      </conclusao>
    </tese>
  </teses>
  
  <fundamentos_juridicos>
    <item>Art. 413, CPP — Pronúncia</item>
    <item>Art. 414, CPP — Impronúncia</item>
    <item>Art. 121, CP — Homicídio</item>
    <item>Súmula 564, STF — In dubio pro reo na pronúncia</item>
  </fundamentos_juridicos>
  
  <dispositivo>
    DAR PROVIMENTO ao recurso para IMPRONUNCIAR o réu, nos termos do art. 414, CPP.
  </dispositivo>
  
  <contexto_processual>
    <item>Caso com forte pressão midiática (vítima era policial)</item>
    <item>Júri popular tende a ser emotivo; impronúncia protege garantias do réu</item>
    <item>Réu sustenta legítima defesa, mas prova é insuficiente para análise aprofundada</item>
  </contexto_processual>
  
  <peculiaridades>
    <item>Única testemunha presencial tem vínculos afetivos com a vítima</item>
    <item>Laudo pericial não conseguiu determinar trajetória do projétil</item>
    <item>Defesa sustenta versão plausível, mas sem prova técnica que a corrobore</item>
  </peculiaridades>
  
  <sensibilidades>
    <item>Caso sensível — impronúncia pode gerar críticas da imprensa e da sociedade</item>
    <item>Necessário reforçar que decisão é técnica e respeita garantias constitucionais</item>
    <item>Linguagem de prelibação obrigatória (Modo Júri ativado)</item>
  </sensibilidades>
  
  <foco_redacional>
    <desafios>
      <desafio type="probatorio">
        <descricao>Prova única (testemunhal) e laudo inconclusivo</descricao>
        <atencao_redacional>
          Demonstrar fragilidade da prova SEM desqualificar testemunha ou MP
        </atencao_redacional>
      </desafio>
      <desafio type="jurisprudencial">
        <descricao>Balancear STF (in dubio pro reo) vs. pressão social por pronúncia</descricao>
        <atencao_redacional>
          Reforçar que decisão é técnica e respeita garantias constitucionais
        </atencao_redacional>
      </desafio>
      <desafio type="processual">
        <descricao>Modo Júri — linguagem de prelibação obrigatória</descricao>
        <atencao_redacional>
          CRÍTICO: usar "indícios", "plausibilidade", "juízo de admissibilidade". Evitar afirmações categóricas sobre autoria/materialidade.
        </atencao_redacional>
      </desafio>
    </desafios>
    
    <requisitos_redacionais>
      <requisito type="estrutura" value="tripartida">
        Relatório + Voto + Dispositivo
      </requisito>
      <requisito type="tom" value="tecnico_cauteloso">
        Tom técnico, cauteloso, sem linguagem inflamada. Respeitar prelibação.
      </requisito>
      <requisito type="extensao" value="longo">
        5-7 páginas (necessário para fundamentação robusta)
      </requisito>
      <requisito type="enfase" value="prova">
        70% do voto deve focar na insuficiência probatória; 30% em refutação de nulidade
      </requisito>
    </requisitos_redacionais>
    
    <estimativas>
      <complexidade>muito_complexa</complexidade>
      <tempo_estimado_minutos>90</tempo_estimado_minutos>
      <nivel_tecnico>alto</nivel_tecnico>
    </estimativas>
  </foco_redacional>
  
  <dossie_referencia version="5.1" url="[path]"/>
  
  <nao_fazer>
    <item>Não produzir ementa</item>
    <item>Não usar linguagem categórica (Modo Júri ativado)</item>
    <item>Não copiar integralmente trechos da sentença</item>
    <item>Não alterar dispositivo canônico</item>
    <item>Não desqualificar testemunha ou MP</item>
  </nao_fazer>
  
  <metrics>
    <blueprint_generation_time_ms>8000</blueprint_generation_time_ms>
    <handoff_generation_time_ms>1800</handoff_generation_time_ms>
    <total_time_ms>9800</total_time_ms>
  </metrics>
  
</kickoff_redator>
```

**Economia:** ~1800 tokens (~35% vs. v4.1, mas com contexto COMPLETO)

---

## [VALIDAÇÃO DO HANDOFF]

### Validação Automática (XML Schema)

O Handoff XML é validado automaticamente contra o XSD v5.1:

```python
import lxml.etree as ET

# Carregar schema
schema = ET.XMLSchema(file="handoff_v5.1.xsd")

# Carregar Handoff XML
handoff = ET.parse("handoff.xml")

# Validar
if schema.validate(handoff):
    print("Handoff válido")
else:
    print("Handoff inválido:")
    for error in schema.error_log:
        print(f"- {error.message}")
```

### Validação Semântica (Maestro)

Após validação estrutural, Maestro valida semântica:

```python
# Validação P6: Blueprint/Handoff válidos
validation = maestro.validate_handoff({
    "xml": handoff_xml,
    "checks": [
        "dispositivo_presente",
        "teses_com_conclusao",
        "fundamentos_juridicos_count >= 1",
        "modo_juri_coerente_com_tipo_peca"
    ]
})

if validation.status == "PASS":
    EMIT_HANDOFF_TO_REDATOR(handoff_xml)
else:
    HALT()
    EMIT_ALERTS(validation.violations)
```

---

## [TROUBLESHOOTING]

### Problema: XML Inválido

**Sintoma:** Erro ao parsear Handoff no Redator

**Diagnóstico:**
```bash
xmllint --schema handoff_v5.1.xsd handoff.xml
```

**Soluções Comuns:**
- Verificar fechamento de tags (`</tese>`, `</item>`, etc.)
- Validar atributos obrigatórios (`numero`, `orgao`, `status`, etc.)
- Confirmar encoding UTF-8
- Verificar valores de enums (`prioridade`, `tipo_peca`, `complexidade`, etc.)

---

### Problema: Contexto Ausente

**Sintoma:** Redator não tem informação sobre peculiaridades do caso

**Diagnóstico:** Analista omitiu `<contexto_processual>` ou `<peculiaridades>`

**Solução:** 
1. Revisar Blueprint para identificar contexto relevante
2. Se houver peculiaridades ou sensibilidades, Analista DEVE incluir no Handoff
3. Se caso é simples, campos podem ser omitidos (opcional)

---

### Problema: Foco Ambíguo

**Sintoma:** Redator não sabe quais desafios priorizar

**Diagnóstico:** `<desafios>` não tem `type` ou descrição clara

**Solução:**
1. Usar `type` correto (`probatorio`, `dosimetrico`, `processual`, `constitucional`, `jurisprudencial`)
2. Preencher `<atencao_redacional>` com orientação clara
3. Ordenar desafios por prioridade (primeiro = mais crítico)

---

## [TESTES DE REGRESSÃO]

### Caso de Teste: Handoff Mínimo (Válido)

**Input:** Caso simples (dosimetria)

**Handoff Esperado:** ~600 tokens, sem contexto opcional

**Validação:**
- XML válido (schema compliance)
- Maestro: PASS
- Campos obrigatórios presentes
- Campos opcionais omitidos

---

### Caso de Teste: Handoff com Modo Júri (Inválido)

**Input:** Caso de homicídio, Modo Júri ativado, mas Handoff tem linguagem categórica

**Handoff Gerado (com erro):**
```xml
<banner_modo_juri enabled="true"/>
<teses>
  <tese id="1" status="acolhida">
    <conclusao>O réu praticou o crime. Pronúncia é medida adequada.</conclusao>
  </tese>
</teses>
```

**Validação Maestro:**
```json
{
  "status": "BLOCKED",
  "violations": [
    {
      "policy_id": "P3",
      "severity": "CRITICAL",
      "description": "Linguagem categórica em Modo Júri: 'O réu praticou o crime'"
    }
  ]
}
```

**Resultado Esperado:** Analista corrige conclusão para linguagem de prelibação.

---

### Caso de Teste: Handoff com Contexto Crítico Omitido

**Input:** Caso complexo (peculiaridade: testemunha única com vínculos afetivos)

**Handoff Gerado (com erro):** Campo `<peculiaridades>` omitido

**Validação Manual:**
```json
{
  "status": "WARNING",
  "message": "Caso complexo com peculiaridade crítica omitida. Adicionar <peculiaridades> para evitar erro redacional."
}
```

**Resultado Esperado:** Analista adiciona `<peculiaridades>` antes de emitir Handoff.

---

## [MÉTRICAS DE OBSERVABILIDADE]

```json
{
  "handoff_metrics": {
    "version": "5.1.0",
    "generation_time_ms": 1200,
    "size_bytes": 2048,
    "validation_status": "valid",
    "modo_juri_activated": false,
    "contexto_presente": true,
    "peculiaridades_count": 2,
    "sensibilidades_count": 2,
    "teses_count": 2,
    "fundamentos_juridicos_count": 5,
    "foco_desafios_count": 2,
    "foco_requisitos_count": 4,
    "estimated_complexity": "media",
    "estimated_redaction_time_minutes": 45,
    "dossie_referenced": true
  }
}
```

---

## [CHANGELOG]

### v5.1.0 (2025-10-19)
- ✅ **[CRITICAL]** Recuperado `<contexto_processual>`, `<peculiaridades>`, `<sensibilidades>` (campos opcionais)
- ✅ **[CRITICAL]** Estruturado `<foco_redacional>` com campos tipados (desafios + requisitos + estimativas)
- ✅ **[HIGH]** Refinado XML Schema (XSD) com validação rigorosa
- ✅ **[MEDIUM]** Adicionados 3 exemplos de uso (simples, médio, complexo + Modo Júri)
- ✅ **[MEDIUM]** Adicionada seção Troubleshooting
- ✅ **[MEDIUM]** Preparado para testes de regressão
- ✅ **[LOW]** Melhorada economia de tokens (campos opcionais preenchidos apenas quando necessário)

### v5.0.0 (2025-10-18)
- Primeira versão com economia brutal (~80 linhas, -70% vs. v4.1)
- XML Schema (XSD) para validação estrutural
- Separação clara de dados processuais, teses, fundamentos, dispositivo

---

**FIM DO DOCUMENTO [D] HANDOFF v5.1**
