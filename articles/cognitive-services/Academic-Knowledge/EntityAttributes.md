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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340184"
---
# <a name="entity-attributes"></a>Atributos de entidade

O gráfico para instituições académicas é composta por 7 tipos de entidade. Todas as entidades terão um ID de entidade e um tipo de entidade.

## <a name="common-entity-attributes"></a>Atributos de entidade comuns
Name    |Descrição                |Tipo       | Operações
------- | ------------------------- | --------- | ----------------------------
Id      |ID de entidade                  |Int64      |É igual a
Ty      |Tipo de entidade                |Enum   |É igual a

## <a name="entity-type-enum"></a>Enumeração de tipo de entidade
Name                                                            |value
----------------------------------------------------------------|-----
[Artigo](PaperEntityAttributes.md)                               |0
[Autor](AuthorEntityAttributes.md)                             |1
[Diário](JournalEntityAttributes.md)                           |2
[Série de conferências](JournalEntityAttributes.md)                 |3
[Instância de conferência](ConferenceInstanceEntityAttributes.md)    |4
[Afiliação](AffiliationEntityAttributes.md)                   |5
[Campo de estudo](FieldsOfStudyEntityAttributes.md)                      |6

