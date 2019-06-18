---
title: Criar atributos de entidade - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Saiba os atributos que pode utilizar com a entidade de autor na API de conhecimento académico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609748"
---
# <a name="author-entity"></a>Entidade de autor
<sub> * Seguintes atributos são específicos para a entidade de autor. (Ty = '1') </sub>

Name    |Descrição                            |Tipo       | Operações
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID de entidade                              |Int64      |É igual a
AuN     |Nome do autor normalizado                 |String     |É igual a
DAuN    |Nome a apresentar do autor                    |String     |Nenhum
CC      |Contagem de total de citação do autor            |Int32      |Nenhum  
ECC     |Contagem de total de citação estimado do autor  |Int32      |Nenhum
E       |Metadados de Extended (consulte a tabela de "Atributos estendidos Meta")  |String     |Nenhum  


## <a name="extended-metadata-attributes"></a>Atributos de metadados expandidos ##

Name    | Descrição               
--------|---------------------------    
LKA. Afn     | nome a apresentar da afiliação associado com o autor  
LKA.AfId        | ID da entidade da afiliação associado com o autor
