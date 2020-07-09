---
title: SDKs de grelha de eventos Azure
description: Descreve os SDKs para Azure Event Grid. Estes SDKs fornecem gestão, publicação e consumo.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: 2b21d8bf2c393b096e9046765e6cfadc76baffbf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119111"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDKs de grelha de eventos para gestão e publicação

O Event Grid fornece SDKs que lhe permitem gerir programaticamente os seus recursos e pós-eventos.

## <a name="management-sdks"></a>SDKs de gestão

Os SDKs de gestão permitem criar, atualizar e eliminar tópicos e subscrições da grelha de eventos. Atualmente, os seguintes SDKs estão disponíveis:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Ir](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Nó](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDKs de avião de dados

Os SDKs do plano de dados permitem-lhe publicar eventos em tópicos, cuidando da autenticação, formando o evento e publicando assíncroticamente no ponto final especificado. Também lhe permitem consumir eventos de primeira festa. Atualmente, os seguintes SDKs estão disponíveis:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Ir](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Nó](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Próximos passos

* Por exemplo, consulte as [amostras de código de Grade de Eventos](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Para uma introdução à Grade de Eventos, veja [o que é a Grade de Eventos?](overview.md)
* Para comandos de Grade de Eventos em Azure [CLI, consulte Azure CLI](/cli/azure/eventgrid).
* Para comandos de Grelha de Eventos em PowerShell, consulte [PowerShell](/powershell/module/az.eventgrid).
