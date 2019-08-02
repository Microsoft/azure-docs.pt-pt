---
title: Atributos de entidade da instância de conferência – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Conheça os atributos que você pode usar com a entidade de instância de conferência na API Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 37a353fbb86ca199b2316dcfba5904f4b46b0276
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705064"
---
# <a name="conference-instance-entity"></a>Entidade de instância de conferência

<sub>* Os atributos a seguir são específicos para a entidade de instância de conferência. (Ty = ' 4 ')</sub>

Nome    |Descrição                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID de entidade                              |Int64      |É igual a
CIN     |Nome normalizado da instância de conferência ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Cadeia     |É igual a
DCN     |Nome de exibição da instância de conferência ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Cadeia     |nenhum
CIL     |Local da instância de conferência    |Cadeia     |Seja<br/>StartsWith
CISD    |Data de início da instância de conferência  |Date       |Seja<br/>IsBetween
CIED    |Data de término da instância de conferência    |Date       |Seja<br/>IsBetween
CIARD   |Data de conclusão do registro abstrato da instância de conferência  |Date       |Seja<br/>IsBetween
CISDD   |Data de vencimento de envio da instância de conferência     |Date       |Seja<br/>IsBetween
CIFVD   |Data de conclusão da versão final da instância de conferência  |Date       |Seja<br/>IsBetween
CINDD   |Data de notificação da instância de conferência   |Date       |Seja<br/>IsBetween
CD. T    |Título de um evento de instância de conferência   |Date       |Seja<br/>IsBetween
CD.D    |Data de um evento de instância de conferência    |Date       |Seja<br/>IsBetween
PCS.CN  |Nome da série de conferências da instância |Cadeia     |É igual a
PCS.CId |ID da série de conferências da instância |Int64    |É igual a
CC      |Contagem de citação total da instância de conferência           |Int32      |nenhum  
CONTROLCENTER     |Contagem de citação total estimada da instância de conferência |Int32      |nenhum


## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

Nome    | Descrição               
--------|---------------------------    
FN      | Nome completo da instância de conferência
