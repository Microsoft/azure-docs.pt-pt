---
title: Entidades nomeadas para cuidados de saúde
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599314"
---
[Análise de texto para](../../how-tos/text-analytics-for-health.md) processos de saúde e extrai insights de dados médicos não estruturados. O serviço deteta e aborda conceitos médicos, atribui afirmações a conceitos, infere relações semânticas entre conceitos e liga-os a ontotologias médicas comuns.

Texto Analytics para a saúde deteta conceitos médicos nas seguintes categorias. Apenas o texto em inglês é suportado nesta pré-visualização e apenas uma versão modelo está disponível.

| Categoria  | Descrição  |
|---------|---------|
| [ANATOMIA](#anatomy) | conceitos que captam informação sobre sistemas corporais e anatômicos, locais, locais ou regiões. |
 | [DEMOGRAFIA](#demographics) | conceitos que captam informação sobre o género e a idade. |
 | [EXAME](#examinations) | conceitos que captam informação sobre procedimentos de diagnóstico e testes. |
 | [ATRIBUTOS GERAIS](#general-attributes) | conceitos que fornecem mais informação sobre outros conceitos das categorias acima. |
 | [GENÓMICA](#genomics) | conceitos que captam informação sobre genes e variantes. |
 | [CUIDADOS DE SAÚDE](#healthcare) | conceitos que captam informação sobre eventos administrativos, ambientes de cuidados e profissões de cuidados de saúde. |
 | [CONDIÇÃO MÉDICA](#medical-condition) | conceitos que captam informação sobre diagnósticos, sintomas ou sinais. |
 | [MEDICAÇÃO](#medication) | conceitos que captam informação sobre medicação, incluindo nomes de medicação, classes, dosagem e rota de administração. |
 | [SOCIAL](#social) | conceitos que captam informação sobre aspetos sociais medicamente relevantes, como a relação familiar. |
 | [TRATAMENTO](#treatment) | conceitos que captam informação sobre procedimentos terapêuticos. |

Encontrará mais informações e exemplos abaixo.

## <a name="anatomy"></a>Anatomia

### <a name="entities"></a>Entidades

**BODY_STRUCTURE** - Sistemas corporais, localizações anatómicas ou regiões, e locais do corpo. Por exemplo, braço, joelho, abdómen, nariz, fígado, cabeça, sistema respiratório, linfócitos.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Um exemplo da entidade da estrutura corporal.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Um exemplo expandido da entidade da estrutura corporal.":::

## <a name="demographics"></a>Demografia

### <a name="entities"></a>Entidades

**IDADE** - Todos os termos e frases de idade, incluindo os para pacientes, familiares, entre outros. Por exemplo, 40 anos, 51, 3 meses de idade, adulto, bebé, idoso, jovem, menor, de meia-idade.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Um exemplo de uma entidade etária.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Outro exemplo de uma entidade etária.":::


**SEXO** - Termos que divulgam o sexo do sujeito. Por exemplo, homem, mulher, mulher, cavalheiro, senhora.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Um exemplo de uma entidade de género.":::

## <a name="examinations"></a>Exames

### <a name="entities"></a>Entidades

**EXAMINATION_NAME** – Procedimentos e testes de diagnóstico, incluindo sinais vitais e medições corporais. Por exemplo, ressonância magnética, ECG, teste de HIV, hemoglobina, contagem de plaquetas, sistemas de escala como *a balança de fezes de Bristol.*

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Um exemplo de uma entidade de exame.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Outro exemplo de uma entidade de nome de exame.":::

## <a name="general-attributes"></a>Atributos gerais

### <a name="entities"></a>Entidades

**DATA** - Data completa relativa a uma condição médica, exame, tratamento, medicação ou evento administrativo.

**DIREÇÃO** – Termos direcionais que podem estar relacionados com uma estrutura corporal, condição médica, exame ou tratamento, tais como: esquerda, lateral, superior, posterior.

**FREQUÊNCIA** - Descreve com que frequência ocorreu uma condição médica, exame, tratamento ou medicação, ou deve ocorrer.

**MEASUREMENT_VALUE** – O valor relacionado com um exame ou uma medição da condição médica.

**MEASUREMENT_UNIT** – A unidade de medição está relacionada com um exame ou uma medição da condição médica.

**RELATIONAL_OPERATOR** - Frases que expressam a relação quantitativa entre uma entidade e algumas informações adicionais.

**TEMPO** - Termos temporais relativos ao início e/ou duração (duração) de uma condição médica, exame, tratamento, medicação ou evento administrativo. 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entidades

**GENE_OR_PROTEIN** – Todas as menções de nomes e símbolos de genes humanos, bem como cromossomas e partes de cromossomas e proteínas. Por exemplo, MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Um exemplo de uma entidade genética.":::

**VARIANTE** – Todas as menções de variações genéticas e mutações. Por `c.524C>T` exemplo, `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Cuidados de saúde

### <a name="entities"></a>Entidades
  
**ADMINISTRATIVE_EVENT** – Eventos que se relacionam com o sistema de saúde mas de natureza administrativa/semi-administrativa. Por exemplo, registo, admissão, ensaio, entrada de estudo, transferência, alta, hospitalização, internamento. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Um exemplo de uma entidade de eventos de saúde.":::

**CARE_ENVIRONMENT** – Um ambiente ou local onde os pacientes recebem cuidados. Por exemplo, urgências, consultório médico, unidade de cardiologia, hospício, hospital.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Esta imagem mostra um exemplo de uma entidade ambiental de saúde.":::

**HEALTHCARE_PROFESSION** – Um profissional de saúde licenciado ou não licenciado. Por exemplo, dentista, patologista, neurologista, radiologista, farmacêutico, nutricionista, fisioterapeuta, quiroprático.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Esta imagem mostra mais um exemplo de uma entidade ambiental de saúde.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Mais um exemplo de uma entidade ambiental de saúde.":::

## <a name="medical-condition"></a>Condição médica

### <a name="entities"></a>Entidades

**DIAGNÓSTICO** - Doença, síndrome, envenenamento. Por exemplo, cancro da mama, Alzheimer, HTN, CHF, lesão da medula espinhal.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Um exemplo de uma entidade de condição médica.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Outro exemplo de uma entidade de condição médica.":::

**SYMPTOM_OR_SIGN** – Evidência subjetiva ou objetiva de doença ou outros diagnósticos. Por exemplo, dor no peito, dor de cabeça, tonturas, erupções cutâneas, SOB, abdómen era macio, bons sons intestinais, bem nutridos.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Um exemplo de um sinal de condição médica ou entidade de sintomas.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Outro exemplo de um sinal de condição médica ou entidade sintomática.":::

**CONDITION_QUALIFIER** - Termos qualitativos que são usados para descrever uma condição médica. Todas as seguintes subcategorias são consideradas qualificativas:

1.  Expressões relacionadas com o tempo: são termos que descrevem qualitativamente a dimensão do tempo, tais como súbita, aguda, crónica, de longa data. 
2.  Expressões de qualidade: São termos que descrevem a "natureza" da condição médica, como a queima, a ponta.
3.  Expressões de severidade: severas, leves, um pouco, descontroladas.
4.  Expressões de extensividade: local, focal, difusa.
5.  Expressões de radiação: irradia, radiação.
6.  Escala de condição: Em alguns casos, uma condição é caracterizada por uma escala, que é uma lista finita ordenada de valores. Por exemplo, doentes com cancro do pâncreas de fase III.
7.  Curso de condição: Um termo que se relaciona com o curso ou progressão de uma condição, tais como melhoria, agravamento, resolução, remissão. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Um exemplo de um atributo de qualificação da condição e uma entidade de diagnóstico.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Outro exemplo de um atributo de qualificação da condição e uma entidade de diagnóstico.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Um exemplo de um atributo de qualificação da condição com entidades de sintomas e medicação.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Esta imagem mostra outro exemplo de um atributo de qualificação da condição com uma entidade de diagnóstico.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Esta imagem mostra um exemplo adicional de um atributo de qualificação da condição com uma entidade de diagnóstico.":::

## <a name="medication"></a>Medicação

### <a name="entities"></a>Entidades

**MEDICATION_CLASS** – Um conjunto de medicamentos que têm um mecanismo de ação semelhante, um modo de ação relacionado, uma estrutura química semelhante, e/ou são usados para tratar a mesma doença. Por exemplo, inibidor ACE, opióide, antibióticos, analgésicos.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Um exemplo de uma entidade da classe de medicação.":::

**MEDICATION_NAME** – Menções de medicação, incluindo nomes de marcas com direitos de autor e nomes não-marcas. Por exemplo, Ibuprofeno.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Um exemplo de uma entidade de nome de medicação.":::

**DOSAGEM** - Quantidade de medicação encomendada. Por exemplo, Infund a solução de Cloreto de *Sódio 1000 mL*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Um exemplo de um atributo de dose de medicação.":::

**MEDICATION_FORM-** A forma da medicação. Por exemplo, solução, pílula, cápsula, tablet, patch, gel, pasta, espuma, spray, gotas, creme, xarope.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Um exemplo de um atributo de formulário de medicação.":::

**MEDICATION_ROUTE** - O método administrativo de medicação. Por exemplo, oral, vaginal, IV, epidural, tópico, inalado.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Um exemplo de um atributo de rota de medicação.":::

## <a name="social"></a>Social

### <a name="entities"></a>Entidades

**FAMILY_RELATION** – Menções de familiares do sujeito. Por exemplo, pai, filha, irmãos, pais.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="A screenshot mostra outro exemplo de um atributo de tempo de tratamento.":::

## <a name="treatment"></a>Tratamento

### <a name="entities"></a>Entidades

**TREATMENT_NAME** – Procedimentos terapêuticos. Por exemplo, cirurgia de substituição do joelho, transplante de medula óssea, TAVI, dieta.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Um exemplo de uma entidade de nome de tratamento.":::
