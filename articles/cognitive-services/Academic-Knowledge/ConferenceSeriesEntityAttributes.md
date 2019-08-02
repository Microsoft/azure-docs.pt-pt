---
title: Atributos de entidade da série de conferências – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que você pode usar com a entidade da série de conferências.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: feed324202f6a75ceb7e9089875b899c51cd8ae6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705049"
---
# <a name="conference-series-entity"></a>Entidade da série de conferências

<sub>* Os atributos a seguir são específicos para a entidade da série de conferências. (Ty = ' 3 ')</sub>

Nome    |Descrição                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID de entidade                              |Int64      |É igual a
CN      |Nome normalizado da série de conferências      |Cadeia     |É igual a
DCN     |Nome de exibição da série de conferências         |Cadeia     |nenhum
CC      |Contagem total de citações da série de conferências         |Int32      |nenhum  
CONTROLCENTER     |Contagem total de citação estimada da série de conferências   |Int32      |nenhum
F.FId   |Campo da ID da entidade de estudo associada à série de conferências |Int64  | É igual a
F.FN    |Campo do nome do estudo associado à série de conferências  | Seja<br/>StartsWith
