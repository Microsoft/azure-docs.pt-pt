---
title: Atributos de entidade de série de conferências - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que pode utilizar com a entidade de série de conferências.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340201"
---
# <a name="conference-series-entity"></a>Entidade de série de conferências

<sub> * Seguintes atributos são específicos para a entidade de série de conferências. (Ty = '3') </sub>

Name    |Descrição                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID de entidade                              |Int64      |Igual a
CN      |Série de conferências normalizados nome      |String     |Igual a
DCN     |Nome de exibição de série de conferências         |String     |nenhum
Cc      |Contagem de citação de total de série de conferências         |Int32      |nenhum  
ECC     |Contagem de total de citação estimado de série de conferência   |Int32      |nenhum
F.FId   |Campo de ID de entidade de estudo associada com a série de conferências |Int64  | Igual a
F.FN    |Campo de nome de estudo associada com a série de conferências  | É igual a,<br/>StartsWith
