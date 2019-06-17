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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340201"
---
# <a name="conference-series-entity"></a>Entidade de série de conferências

<sub> * Seguintes atributos são específicos para a entidade de série de conferências. (Ty = '3') </sub>

Name    |Descrição                            |Tipo       | Operações
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID de entidade                              |Int64      |É igual a
CN      |Série de conferências normalizados nome      |String     |É igual a
DCN     |Nome de exibição de série de conferências         |String     |Nenhum
CC      |Contagem de citação de total de série de conferências         |Int32      |Nenhum  
ECC     |Contagem de total de citação estimado de série de conferência   |Int32      |Nenhum
F.FId   |Campo de ID de entidade de estudo associada com a série de conferências |Int64  | É igual a
F.FN    |Campo de nome de estudo associada com a série de conferências  | É igual a,<br/>StartsWith
