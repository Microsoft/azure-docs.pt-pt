---
title: SDKs de grelha de eventos do Azure
description: Descreve os SDKs do Azure Event Grid. Estes SDKs fornecem gerenciamento, publicação e de consumo.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58173758"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDKs de grelha de eventos para gerenciamento e publicação

Grelha de eventos disponibiliza SDKs que permitem-lhe gerir os recursos e publicar eventos através de programação.

## <a name="management-sdks"></a>SDKs de gestão

A gestão SDKs permitem-lhe criar, atualizar e eliminar subscrições e tópicos do event grid. Atualmente, os SDKs seguintes estão disponíveis:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDKs do plano de dados

SDKs de ao plano de dados permitem-lhe publicar eventos para tópicos, com a autenticação, formando o evento e lançamento de forma assíncrona para o ponto final especificado. Eles também permitem consumir eventos de terceiros primeiro. Atualmente, os SDKs seguintes estão disponíveis:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Passos Seguintes

* Por exemplo aplicações, veja [exemplos de código do Event Grid](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Para obter uma introdução ao Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter comandos na CLI do Azure Event Grid, veja [CLI do Azure](/cli/azure/eventgrid).
* Para obter comandos Event Grid no PowerShell, veja [PowerShell](/powershell/module/az.eventgrid).
