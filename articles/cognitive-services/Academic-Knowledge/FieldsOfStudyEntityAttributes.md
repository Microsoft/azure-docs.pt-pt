---
title: Atributos de entidade de campo de estudo - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Saiba os atributos que pode utilizar com a entidade de campo de estudo na API de conhecimento académico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61339590"
---
# <a name="field-of-study-entity"></a>Campo de entidade de estudo

<sub> * Seguintes atributos são específicos para o campo de estudo de entidade. (Ty = '6') </sub>

Name    |Descrição                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID de entidade                              |Int64      |Igual a
FN      |Campo de nome de normalizado de estudo         |String     |Igual a
DFN     |Campo de nome de exibição de estudo            |String     |nenhum
Cc      |Campo de contagem de total de citação de estudo    |Int32      |nenhum  
ECC     |Campo de contagem de total de citação estimado|Int32      |nenhum
FL      |Nível de campos de hierarquia de estudo     |Int32      |É igual a, <br/>IsBetween
FP.FN   |Campo de principal do nome de estudo             |String     |Igual a
FP.FId  |Campo de principal do ID de estudo               |Int64      |Igual a
FC.FN   |Campo de subordinados do nome de estudo              |String     |Igual a
FC.FId  |Campo de subordinado de ID de estudo                |Int64      |Igual a
