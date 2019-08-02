---
title: Campo de atributos de entidade de estudo-API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Conheça os atributos que você pode usar com o campo de entidade de estudo na API Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d54f3c72462e6702b09068092b7c18ea50f12048
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704983"
---
# <a name="field-of-study-entity"></a>Campo da entidade de estudo

<sub>* Os atributos a seguir são específicos para o campo da entidade de estudo. (Ty = ' 6 ')</sub>

Nome    |Descrição                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID de entidade                              |Int64      |É igual a
FN      |Campo do nome normalizado do estudo         |Cadeia     |É igual a
DFN     |Campo do nome de exibição do estudo            |Cadeia     |nenhum
CC      |Campo da contagem total de citações de estudo    |Int32      |nenhum  
CONTROLCENTER     |Campo da contagem total de citação estimada|Int32      |nenhum
FLÓRIDA      |Nível nos campos da hierarquia de estudo     |Int32      |Seja <br/>IsBetween
FP.FN   |Campo pai do nome do estudo             |Cadeia     |É igual a
FP.FId  |Campo pai da ID do estudo               |Int64      |É igual a
FC.FN   |Campo filho do nome do estudo              |Cadeia     |É igual a
FC.FId  |Campo filho da ID do estudo                |Int64      |É igual a
