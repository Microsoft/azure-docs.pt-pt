---
title: Entidades nomeadas para cuidados de saúde
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122357"
---
## <a name="health-entity-categories"></a>Categorias de entidades sanitárias:

As seguintes categorias de entidades são devolvidas pela [Text Analytics para a saúde.](../../how-tos/text-analytics-for-health.md)  Tenha em atenção que apenas o texto em inglês é suportado nesta pré-visualização do recipiente e apenas uma única versão modelo é fornecida em cada imagem de recipiente.

### <a name="named-entity-recognition"></a>Reconhecimento de Entidades Nomeadas

|Categoria  |Descrição   |
|----------|--------------|
| Idade | Idades. Por *exemplo, 30 anos*de idade. |
| Evento Administrativo | Um evento administrativo. |
| Estrutura Corporal | Partes do corpo humano, incluindo órgãos e outras estruturas. Por *exemplo, braço*, ou *coração*. | 
| CareEnvironment | O ambiente onde os cuidados ou tratamentos são administrados. Por *exemplo, as urgências* | 
| Condicionável | Níveis de condição. Por exemplo, *leve,* *estendida,* ou *difusa.* | 
| Diagnóstico | Condições médicas. Por *exemplo, hipertensão*. | 
| Direção | Trajeto. Por *exemplo, à esquerda* ou *anterior.* | 
| Dosagem | Tamanho ou quantidade de um medicamento. Por *exemplo, 25mg*.  | 
| Exame Nome | Um método ou procedimento de exame. Por *exemplo, raio-X.* | 
| Operador relacional | Um operador que define uma relação entre duas entidades. Por exemplo, *menos do que*, ou . `>=` .  | 
| Unidade de Medição | Uma unidade de medição (como uma percentagem). | 
| Valor da Medição | O valor numérico de uma unidade de medição. | 
| FamilyRelation | Uma relação familiar. Por *exemplo, irmã.*  | 
| Frequência | Frequências, frequências.   | 
| Sexo | Os sexos. | 
| Gene | Uma entidade genética como *a TP53.*   | 
| HealthcareProfession | Método de administração de medicação. Por *exemplo, administração oral.* | 
| Classe de Medicação | Aulas de medicação. Por *exemplo, antibióticos.*  | 
| MedicaçãoForm | Uma forma de medicação. Por *exemplo, cápsula.* | 
| Nome da Medicação  | Medicamentos genéricos e de marca. Por *exemplo, ibuprofeno*. | 
| MedicaçãoRoute | Método de administração de medicação. Por *exemplo, administração oral.* | 
| SintomaOrSign  | Sintomas de doença. Por *exemplo, dor de garganta.* | 
| Hora | Os tempos. Por *exemplo, 8 anos* ou *2:30 esta manhã* |
| Nome de tratamento  | Nomes de tratamento. Por *exemplo, terapia*. | 
| Variant | Uma variante genética da entidade genética. | 

### <a name="relation-extraction"></a>Extração de relação

A extração de relação identifica ligações significativas entre conceitos mencionados em texto. Por exemplo, uma relação de "tempo de condição" é encontrada associando um nome de condição com uma hora. Texto Analytics para a saúde pode identificar as seguintes relações:

|Categoria  |Descrição   |
|----------|--------------|
| DirectionOfBodyStructure | Direção de uma estrutura corporal. |
| Modo de Instrução | Direção de uma condição. |
| DireçõesOfExaminação | Direção de um exame. |
| DireçãoOfTratamento | Direção de um tratamento. |
| TempoOfCondição | O tempo associado ao início de uma condição. |
| EliminatóriasCondição | A qualificação associada para uma condição. |
| DosagemOfMedication | Uma dose de medicação. |
| FormaOfMedicação | Uma forma de medicação. |
| RouteOfMedication | Uma rota ou modo de consumir um medicamento. Por *exemplo, oral.* |
| FrequênciaOfMedication | A frequência com que um medicamento é consumido. | 
| ValorOfCondição | Um valor numérico associado a uma condição. |
| UnitOfCondição | Uma unidade (como o tempo) associada a uma condição. |
| TimeOfMedication | O momento em que um medicamento foi consumido. |
| TempoOfTratamento | O momento em que um tratamento foi administrado. | 
| FrequênciaOfTreatment | A frequência com que um tratamento é administrado. |
| ValorOfExaminação | Um valor numérico associado a um exame. | 
| UnitOfExamination | Uma unidade (por exemplo, uma percentagem) associada a um exame. |
| RelaçãoOfExamination | Uma relação entre uma entidade e um exame. | 
| TimeOfExamination | O tempo associado a um exame. |
| Abreviatura | Uma abreviatura.  | 
