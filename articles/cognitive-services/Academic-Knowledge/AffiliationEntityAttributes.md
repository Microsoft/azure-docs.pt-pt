---
title: Atributos de entidade de afiliação na API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Conheça os atributos que você pode usar com a entidade de afiliação na API Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705104"
---
# <a name="affiliation-entity"></a>Entidade de afiliação

<sub>* Os atributos a seguir são específicos para a entidade de afiliação. (Ty = ' 5 ')</sub>

Nome    |Descrição                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID de entidade                              |Int64      |É igual a
AfN     |Nome normalizado de afiliação        |Cadeia     |É igual a
DAfN    |Nome de exibição da afiliação       |Cadeia     |nenhum
CC      |Contagem de citação total de afiliação           |Int32      |nenhum  
CONTROLCENTER     |Contagem total de citação estimada de afiliação |Int32      |nenhum

## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

Nome    | Descrição               
--------|---------------------------    
PC      |Contagem de papel da afiliada
