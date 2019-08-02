---
title: Atributos de entidade do grafo acadêmico – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos de entidade que você pode usar com o grafo acadêmico na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705025"
---
# <a name="entity-attributes"></a>Atributos de entidade

O grafo acadêmico é composto por sete tipos de entidade. Todas as entidades terão uma ID de entidade e um tipo de entidade.

## <a name="common-entity-attributes"></a>Atributos comuns de entidade
Nome    |Descrição                |Type       | Operações
------- | ------------------------- | --------- | ----------------------------
ID      |ID de entidade                  |Int64      |É igual a
Ty      |Tipo de entidade                |Enum   |É igual a

## <a name="entity-type-enum"></a>Enumeração de tipo de entidade
Nome                                                            |value
----------------------------------------------------------------|-----
[Artigo](PaperEntityAttributes.md)                               |0
[Autor](AuthorEntityAttributes.md)                             |1
[Diário](JournalEntityAttributes.md)                           |2
[Série de conferências](JournalEntityAttributes.md)                 |3
[Instância de conferência](ConferenceInstanceEntityAttributes.md)    |4
[Afiliação](AffiliationEntityAttributes.md)                   |5
[Campo de estudo](FieldsOfStudyEntityAttributes.md)                      |6

