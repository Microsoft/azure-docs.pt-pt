---
title: Gerir consultas de caça e livestream em Azure Sentinel usando REST API | Microsoft Docs
description: Este artigo descreve como as funcionalidades de caça Azure Sentinel permitem-lhe tirar partido da API REST do Log Analytics para gerir consultas de caça e livestream.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 64f05e18ff757d9f086cf06d74109bf64e32a05c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795683"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Gerir consultas de investigação e transmissão em direto no Azure Sentinel com a API REST

O Azure Sentinel, sendo construído em parte no Azure Monitor Log Analytics, permite-lhe utilizar a API REST do Log Analytics para gerir consultas de caça e livestream. Este documento mostra-lhe como criar e gerir consultas de caça utilizando a API REST.  As consultas criadas desta forma serão exibidas na UI Azure Sentinel.

Consulte a referência definitiva da API REST para obter mais detalhes sobre a [API de pesquisas guardadas.](/rest/api/loganalytics/savedsearches)

## <a name="api-examples"></a>Exemplos de API

Nos seguintes exemplos, substitua estes espaços reservados pela substituição prescrita no quadro seguinte:

| Marcador de posição | Substituir |
|-|-|
| **{subscriçãoD}** | o nome da subscrição a que está a aplicar a consulta de caça ou livestream. |
| **{recursoGroupName}** | o nome do grupo de recursos a que está a aplicar a consulta de caça ou livestream. |
| **{salvoSearchId}** | um id único (GUID) para cada consulta de caça. |
| **{WorkspaceName}** | o nome do espaço de trabalho Log Analytics que é o alvo da consulta. |
| **{DisplayName}** | um nome de exibição da sua escolha para a consulta. |
| **{Descrição}** | uma descrição da consulta de caça ou livestream. |
| **{Táticas}** | as táticas CK&CK relevantes do MITRE que se aplicam à consulta. |
| **{Consulta}** | a expressão de consulta para a sua consulta. |
|  

### <a name="example-1"></a>Exemplo 1

Este exemplo mostra-lhe como criar ou atualizar uma consulta de caça para um determinado espaço de trabalho Azure Sentinel.  Para uma consulta em livestream, substitua *"Categoria": "Consultas de caça"* por *"Categoria": "Consultas livestream"* no **organismo de pedido:** 

#### <a name="request-header"></a>Cabeçalho do pedido

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Corpo do pedido

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Exemplo 2

Este exemplo mostra-lhe como eliminar uma consulta de caça ou livestream para um determinado espaço de trabalho Azure Sentinel:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Exemplo 3

Este exemplo mostra-lhe para recuperar uma consulta de caça ou livestream para um determinado espaço de trabalho:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a gerir consultas de caça e livestream em Azure Sentinel usando a API log Analytics. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- [Caça proativamente por ameaças](hunting.md)
- [Use cadernos para executar campanhas de caça automatizadas](notebooks.md)