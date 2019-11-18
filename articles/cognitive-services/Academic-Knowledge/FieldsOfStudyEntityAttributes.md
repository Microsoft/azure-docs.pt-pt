---
title: Campo de atributos de entidade de estudo-API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Conheça os atributos que você pode usar com o campo de entidade de estudo na API Conhecimento Acadêmico.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146485"
---
# <a name="field-of-study-entity"></a>Campo da entidade de estudo

> [!NOTE]
> Os atributos a seguir são específicos para o campo da entidade de estudo. (Ty = ' 6 ')

Nome | Descrição | Tipo | Operações
--- | --- | --- | ---
CC      |Campo da contagem total de citações de estudo    |Int32      |Nenhum  
DFN     |Campo do nome de exibição do estudo            |Cadeia     |Nenhum
ControlCenter     |Campo da contagem total de citação estimada|Int32      |Nenhum
Flórida      |Nível nos campos da hierarquia de estudo     |Int32      |Equals, isBetween
FN      |Campo do nome normalizado do estudo         |Cadeia     |Igual a
FC.FId  |Campo filho da ID do estudo                |Int64      |Igual a
FC.FN   |Campo filho do nome do estudo              |Cadeia     |Igual a
FP.FId  |Campo pai da ID do estudo               |Int64      |Igual a
FP.FN   |Campo pai do nome do estudo             |Cadeia     |Igual a
Id      |ID da entidade                              |Int64      |Igual a
PC    | Campo da contagem de publicação total de estudos | Int32 | Nenhum
