---
title: Atributos de entidade do autor-API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Conheça os atributos que você pode usar com a entidade autor na API Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705094"
---
# <a name="author-entity"></a>Entidade de autor
<sub>* Os atributos a seguir são específicos para a entidade de autor. (Ty = ' 1 ')</sub>

Nome    |Descrição                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID de entidade                              |Int64      |É igual a
AuN     |Nome normalizado do autor                 |Cadeia     |É igual a
DAuN    |Nome de exibição do autor                    |Cadeia     |nenhum
CC      |Contagem total de citações do autor            |Int32      |nenhum  
CONTROLCENTER     |Autor total estimado contagem de citação  |Int32      |nenhum
E       |Metadados estendidos (consulte a tabela "atributos meta estendidos")  |Cadeia     |nenhum  


## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

Nome    | Descrição               
--------|---------------------------    
LKA. Afn     | nome de exibição da afiliação associado ao autor  
LKA. AfId        | ID da entidade da afiliação associada ao autor
