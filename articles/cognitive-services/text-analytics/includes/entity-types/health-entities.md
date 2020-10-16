---
title: Entidades nomeadas para cuidados de saúde
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 44bce7c6dd2443ea0ed1851325a5b84f325f5fae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779856"
---
## <a name="text-analytics-for-health-categories-entities-and-attributes"></a>Análise de Texto para categorias de saúde, entidades e atributos

[Texto Analytics para a saúde](../../how-tos/text-analytics-for-health.md) deteta conceitos médicos nas seguintes categorias.  (Tenha em atenção que apenas o texto inglês é suportado nesta pré-visualização do recipiente e apenas uma única versão modelo é fornecida em cada imagem de recipiente.)


| Categoria  | Descrição  |
|---------|---------|
| ANATOMIA | conceitos que captam informação sobre sistemas, sites, locais ou regiões do corpo e anatômicos. |
 | DEMOGRAFIA | conceitos que captam informação sobre o género e a idade. |
 | EXAME | conceitos que captam informação sobre procedimentos de diagnóstico e testes. |
 | GENÓMICA | conceitos que captam informação sobre genes e variantes. |
 | CUIDADOS DE SAÚDE | conceitos que captam informação sobre eventos administrativos, ambientes de cuidados e profissões de saúde. |
 | CONDIÇÃO MÉDICA | conceitos que captam informação sobre diagnósticos, sintomas ou sinais. |
 | MEDICAÇÃO | conceitos que captam informação sobre medicação, incluindo nomes de medicação, classes, dosagem e rota de administração. |
 | SOCIAL | conceitos que captam informação sobre aspetos sociais medicamente relevantes, como a relação familiar. |
 | TRATAMENTO | conceitos que captam informação sobre procedimentos terapêuticos. |
  
Cada categoria pode incluir dois grupos de conceito:

* **Entidades** - termos que captam conceitos médicos como diagnóstico, nome da medicamentação ou nome do tratamento.  Por exemplo, *bronquite* é um diagnóstico e *aspirina* é um nome de medicação.  As menções neste grupo podem estar ligadas a um ID do conceito UMLS.
* **Atributos** - frases que fornecem mais informações sobre a entidade detetada, por exemplo, *grave* é uma condição de qualificação para *bronquite* ou *81 mg* é uma dose para *aspirina*.  As menções nesta categoria NÃO estarão ligadas a um ID do conceito UMLS.

Adicionalmente, o serviço reconhece as relações entre os diferentes conceitos, incluindo as relações entre atributos e entidades, por exemplo, *direção* à *estrutura corporal* ou *dosagem* ao nome da *medicação* e as relações entre entidades, por exemplo, na deteção de abreviaturas.

## <a name="anatomy"></a>Anatomia

### <a name="entities"></a>Entidades

**BODY_STRUCTURE** - Sistemas corporais, localizações anatómicas ou regiões, e locais do corpo. Por exemplo, braço, joelho, abdómen, nariz, fígado, cabeça, sistema respiratório, linfócitos.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Um exemplo da entidade da estrutura corporal.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="attributes"></a>Atributos

**DIREÇÃO** - Termos direcionais, tais como: esquerda, lateral, superior, posterior, que caracteriza uma estrutura corporal.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="supported-relations"></a>Relações apoiadas

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>Demografia

### <a name="entities"></a>Entidades

**IDADE** - Todos os termos e frases de idade, incluindo os de um paciente, membros da família, e outros. Por exemplo, 40 anos, 51, 3 meses de idade, adulto, bebé, idoso, jovem, menor, de meia-idade.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Um exemplo da entidade da estrutura corporal.":::


**SEXO** - Termos que divulgam o sexo do sujeito. Por exemplo, homem, mulher, mulher, cavalheiro, senhora.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="attributes"></a>Atributos

**RELATIONAL_OPERATOR** - Frases que exprimem a relação entre uma entidade demográfica e informações adicionais.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

## <a name="examinations"></a>Exames

### <a name="entities"></a>Entidades

**EXAMINATION_NAME** – Procedimentos e testes de diagnóstico. Por exemplo, ressonância magnética, ECG, teste de HIV, hemoglobina, contagem de plaquetas, sistemas de escala como *a balança de fezes de Bristol.*

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="attributes"></a>Atributos

**DIREÇÃO** – Termos direcionais que caracterizam um exame.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**MEASUREMENT_UNIT** – A unidade do exame. Por exemplo, na *hemoglobina > 9,5 g/dL,* o termo *g/dL* é a unidade para o teste de *hemoglobina.*

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**MEASUREMENT_VALUE** – O valor do exame. Por exemplo, na *hemoglobina > 9,5 g/dL,* o termo *9.5* é o valor para o teste de *hemoglobina.*

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**RELATIONAL_OPERATOR** – Frases que exprimem a relação entre um exame e informações adicionais. Por exemplo, o valor de medição necessário para um exame de destino.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**TEMPO** – Termos temporais relativos ao início e/ou duração (duração) de um exame. Por exemplo, quando ocorreu o teste.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="supported-relations"></a>Relações apoiadas

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entidades

**GENE** – Todas as menções de genes. Por exemplo, MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**VARIANTE** – Todas as menções de variações genéticas. Por exemplo, c.524C>T, (MTRR):r.1462_1557del96
  
## <a name="healthcare"></a>Cuidados de saúde

### <a name="entities"></a>Entidades
  
**ADMINISTRATIVE_EVENT** – Eventos que se relacionam com o sistema de saúde mas de natureza administrativa/semi-administrativa. Por exemplo, registo, admissão, ensaio, entrada de estudo, transferência, alta, hospitalização, internamento. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**CARE_ENVIRONMENT** – Um ambiente ou local onde os pacientes recebem cuidados. Por exemplo, urgências, consultório médico, unidade de cardiologia, hospício, hospital.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**HEALTHCARE_PROFESSION** – Um profissional de saúde licenciado ou não licenciado. Por exemplo, dentista, patologista, neurologista, radiologista, farmacêutico, nutricionista, fisioterapeuta, quiroprático.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

## <a name="medical-condition"></a>Condição médica

### <a name="entities"></a>Entidades

**DIAGNÓSTICO** - Doença, síndrome, envenenamento. Por exemplo, cancro da mama, Alzheimer, HTN, CHF, lesão da medula espinhal.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**SYMPTOM_OR_SIGN** – Evidência subjetiva ou objetiva de doença ou outros diagnósticos. Por exemplo, dor no peito, dor de cabeça, tonturas, erupções cutâneas, SOB, abdómen era macio, bons sons intestinais, bem nutridos.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="attributes"></a>Atributos

**CONDITION_QUALIFIER** - Termos de qualidade que são usados para descrever uma condição médica. Todas as sub-categorias seguintes são consideradas qualificativas:

1.  Expressões relacionadas com o tempo: são termos que descrevem qualitativamente a dimensão do tempo, tais como súbita, aguda, crónica, de longa data. 
2.  Expressões de qualidade: São termos que descrevem a "natureza" da condição médica, como a queima, a ponta.
3.  Expressões de severidade: severas, leves, um pouco, descontroladas.
4.  Expressões de extensividade: local, focal, difusa.
5.  Expressões de radiação: irradia, radiação.
6.  Escala de condição: Em alguns casos, uma condição é caracterizada por uma escala, que é uma lista finita ordenada de valores. Por exemplo, doentes com cancro do pâncreas de fase III.
7.  Curso de condição: Um termo que se relaciona com o curso ou progressão de uma condição, tais como melhoria, agravamento, resolução, remissão. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**DIREÇÃO** - Termos direcionais que caracterizam uma condição médica corporal.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**FREQUÊNCIA** - Quantas vezes ocorreu uma condição médica, ocorre ou deve ocorrer.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**MEASUREMENT_UNIT** - A unidade que caracteriza uma condição médica. Por exemplo, no *tumor de 1,5x2x1 cm,* o termo *cm* é a unidade de medição do *tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**MEASUREMENT_VALUE** - O valor que caracteriza uma condição médica. Por exemplo, no *tumor de 1,5x2x1 cm,* o termo *1,5x2x1* é o valor de medição do *tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**RELATIONAL_OPERATOR** - Frases que expressam a relação entre a condição médica de informação adicional. Por exemplo, o tempo ou o valor da medição. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**TEMPO** - Termos temporais relativos ao início e/ou duração (duração) de uma condição médica. Por exemplo, quando um sintoma começou (início) ou quando ocorreu uma doença.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="supported-relations"></a>Relações apoiadas

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>Medicação

### <a name="entities"></a>Entidades

**MEDICATION_CLASS** – Um conjunto de medicamentos que têm um mecanismo de ação semelhante, um modo de ação relacionado, uma estrutura química semelhante, e/ou são usados para tratar a mesma doença. Por exemplo, inibidor ACE, opióide, antibióticos, analgésicos.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**MEDICATION_NAME** – Menções de medicação, incluindo nomes de marcas com direitos de autor e nomes não-marcas. Por exemplo, Advil, Ibuprofeno.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="attributes"></a>Atributos

**DOSAGEM** - Quantidade de medicação encomendada. Por exemplo, Infund a solução de Cloreto de *Sódio 1000 mL*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**FREQUÊNCIA** - Quantas vezes deve ser tomado um medicamento.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**MEDICATION_FORM-** A forma da medicação. Por exemplo, solução, pílula, cápsula, tablet, patch, gel, pasta, espuma, spray, gotas, creme, xarope.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**MEDICATION_ROUTE** - O método administrativo de medicação. Por exemplo, oral, vaginal, IV, epidural, tópico, inalado.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**RELATIONAL_OPERATOR** - Frases que expressam a relação entre a medicação e a informação adicional. Por exemplo, o valor de medição necessário.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="supported-relations"></a>Relações apoiadas

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**
  
## <a name="treatment"></a>Tratamento

### <a name="entities"></a>Entidades

**TREATMENT_NAME** – Procedimentos terapêuticos. Por exemplo, cirurgia de substituição do joelho, transplante de medula óssea, TAVI, dieta.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

### <a name="attributes"></a>Atributos

**DIREÇÃO** - Termos direcionais que caracterizam um tratamento.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**FREQUÊNCIA** - Com que frequência ocorre ou deve ocorrer um tratamento.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="Um exemplo da entidade da estrutura corporal.":::
 
**RELATIONAL_OPERATOR** - Frases que expressam a relação entre o tratamento e a informação adicional.  Por exemplo, quanto tempo passou do procedimento anterior.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Um exemplo da entidade da estrutura corporal.":::

**TEMPO** - Termos temporais relativos ao início e/ou duração (duração) de um tratamento. Por exemplo, a data em que o tratamento foi dado.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="Um exemplo da entidade da estrutura corporal.":::


### <a name="supported-relations"></a>Relações apoiadas

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**

## <a name="social"></a>Social

### <a name="entities"></a>Entidades

**FAMILY_RELATION** – Menções de familiares do sujeito. Por exemplo, pai, filha, irmãos, pais.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Um exemplo da entidade da estrutura corporal.":::
