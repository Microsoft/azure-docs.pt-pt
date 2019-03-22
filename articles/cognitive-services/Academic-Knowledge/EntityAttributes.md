---
title: Atributos de entidade Academic Graph - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos de entidade que pode utilizar com o gráfico para instituições académicas na API de conhecimento académico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183497"
---
# <a name="entity-attributes"></a>Atributos de entidade

O gráfico para instituições académicas é composta por 7 tipos de entidade. Todas as entidades terão um ID de entidade e um tipo de entidade.

## <a name="common-entity-attributes"></a>Atributos de entidade comuns
Name    |Descrição                |Type       | Operações
------- | ------------------------- | --------- | ----------------------------
Id      |ID de entidade                  |Int64      |Igual a
Ty      |Tipo de entidade                |Enum   |Igual a

## <a name="entity-type-enum"></a>Enumeração de tipo de entidade
Name                                                            |valor
----------------------------------------------------------------|-----
[Artigo](PaperEntityAttributes.md)                               |0
[Autor](AuthorEntityAttributes.md)                             |1
[Diário](JournalEntityAttributes.md)                           |2
[Série de conferências](JournalEntityAttributes.md)                 |3
[Instância de conferência](ConferenceInstanceEntityAttributes.md)    |4
[Afiliação](AffiliationEntityAttributes.md)                   |5
[Campo de estudo](FieldsOfStudyEntityAttributes.md)                      |6

