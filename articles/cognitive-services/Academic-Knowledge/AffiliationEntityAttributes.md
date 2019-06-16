---
title: Atributos de entidade de afiliação na API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Saiba os atributos que pode utilizar com a entidade de afiliação na API de conhecimento académico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340524"
---
# <a name="affiliation-entity"></a>Afiliação de entidade

<sub> * Seguintes atributos são específicos para a entidade de afiliação. (Ty = '5') </sub>

Name    |Descrição                            |Tipo       | Operações
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID de entidade                              |Int64      |É igual a
AfN     |Nome de afiliação normalizado        |String     |É igual a
DAfN    |Nome a apresentar afiliação       |String     |Nenhum
CC      |Contagem de total citation de afiliação           |Int32      |Nenhum  
ECC     |Contagem de total de citação estimado afiliação |Int32      |Nenhum

## <a name="extended-metadata-attributes"></a>Atributos de metadados expandidos ##

Name    | Descrição               
--------|---------------------------    
PC      |Contagem de papel da afiliação
