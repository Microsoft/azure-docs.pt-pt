---
title: SDKs da grelha de eventos azure
description: Descreve os SDKs para a Grelha de Eventos Azure. Estes SDKs fornecem gestão, publicação e consumo.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60822834"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDKs de Grelha de Eventos para gestão e publicação

A Event Grid fornece SDKs que lhe permitem gerir programáticamente os seus recursos e pós-eventos.

## <a name="management-sdks"></a>SDKs de gestão

Os SDKs de gestão permitem-lhe criar, atualizar e eliminar tópicos e subscrições da grelha de eventos. Atualmente, estão disponíveis os seguintes SDKs:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Ir](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDKs de avião de dados

Os SDKs do plano de dados permitem-lhe publicar eventos em tópicos, cuidando da autenticação, formando o evento e publicando assincronicamente para o ponto final especificado. Também lhe permitem consumir eventos de primeira festa. Atualmente, estão disponíveis os seguintes SDKs:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Ir](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Passos seguintes

* Por exemplo, aplicações, consulte [amostras de código da Grelha de Eventos](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Para uma introdução à Grelha de Eventos, veja [o que é a Grelha de Eventos?](overview.md)
* Para comandos da Rede de Eventos em Azure CLI, consulte [Azure CLI](/cli/azure/eventgrid).
* Para comandos da Rede de Eventos no PowerShell, consulte [PowerShell](/powershell/module/az.eventgrid).
