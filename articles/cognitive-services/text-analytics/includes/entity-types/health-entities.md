---
title: Entidades nomeadas para cuidados de saúde
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/15/2020
ms.author: aahi
ms.openlocfilehash: 906e8e9e6cde80a2151c6b8671f64854cb431250
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108512"
---
## <a name="health-entity-categories"></a>Categorias de entidades sanitárias:

As seguintes categorias de entidades são devolvidas por [Text Analytics for Health](../../how-tos/text-analytics-for-health.md).  Tenha em atenção que apenas o texto em inglês é suportado nesta pré-visualização do recipiente e apenas uma única versão modelo é fornecida em cada imagem de recipiente.

### <a name="named-entity-recognition"></a>Reconhecimento de Entidades Nomeadas

|Categoria  |Descrição   |
|----------|--------------|
| IDADE | Idades. |
| BODY_STRUCTURE | Partes do corpo humano, incluindo órgãos e outras estruturas. | 
| CONDITION_QUALIFIER | Níveis de condição tais como *leve,* *prolongado*ou *difuso*. | 
| DIAGNÓSTICO | Condições médicas. Por *exemplo, hipertensão* . | 
| DIREÇÃO | Direções como *esquerda* ou *anterior.* | 
| DOSAGEM | Tamanho ou quantidade de um medicamento.  | 
| EXAMINATION_NAME | Um método ou procedimento de exame. | 
| EXAMINATION_RELATION | uma associação entre uma unidade de medição e um exame.  | 
| EXAMINATION_UNIT | Uma unidade de medição para um exame. | 
| EXAMINATION_VALUE | O valor da unidade de medição do exame. | 
| FAMILY_RELATION | Uma relação familiar, como a *irmã.*  | 
| FREQUÊNCIA | Frequências, frequências.   | 
| Género | Os sexos. | 
| GENE | Uma entidade genética como *a TP53.*   | 
| MEDICATION_CLASS | Aulas de medicação. Por *exemplo, antibióticos.*  | 
| MEDICATION_NAME  | Medicamentos genéricos e de marca.| 
| ROUTE_OR_MODE  | Método de administração de medicação. | 
| SYMPTOM_OR_SIGN  | Sintomas de doença. | 
| HORA  | Os tempos. Por exemplo" "8 anos" ou "2:30 desta manhã" |
| TREATMENT_NAME  | Nomes de tratamento. | 
| VARIANTE  | Uma variante genética da entidade genética | 

### <a name="relation-extraction"></a>Extração de relação

A extração de relação identifica ligações significativas entre conceitos mencionados em texto. Por exemplo, uma relação de "tempo de condição" é encontrada associando um nome de condição com uma hora. Texto Analytics para a saúde pode identificar as seguintes relações:

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* ABREVIATURA 
