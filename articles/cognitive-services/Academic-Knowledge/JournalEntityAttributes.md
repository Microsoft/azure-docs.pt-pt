---
title: Atributos de entidade do diário - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Saiba os atributos que pode utilizar com a entidade de diário na API de conhecimento académico nos serviços cognitivos.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ffb159dc684b4b6663dcb966706d4745ab88a403
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61337810"
---
# <a name="journal-entity"></a>Entidade de diário

<sub> * Seguintes atributos são específicos para a entidade de diário. (Ty = '2') </sub>

Name    |Descrição                            |Tipo       | Operações
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID de entidade                              |Int64      |É igual a
DJN     |Nome do diário normalizado                |String     |Nenhum
JN      |Nome a apresentar do diário                   |String     |É igual a
CC      |Contagem de total de citação de diário           |Int32      |Nenhum  
ECC     |Contagem de total de citação estimado de diário |Int32      |Nenhum
