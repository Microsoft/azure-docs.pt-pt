---
title: SDKs de grelha de eventos Azure
description: Descreve os SDKs para Azure Event Grid. Estes SDKs fornecem gestão, publicação e consumo.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: aa53aaa89a703bb88df6611cdb0dd38341248a55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955929"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDKs de grelha de eventos para gestão e publicação

O Event Grid fornece SDKs que lhe permitem gerir programaticamente os seus recursos e pós-eventos.

## <a name="management-sdks"></a>SDKs de gestão

Os SDKs de gestão permitem criar, atualizar e eliminar tópicos e subscrições da grelha de eventos. Atualmente, os seguintes SDKs estão disponíveis:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Ir](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Nó](https://www.npmjs.com/package/@azure/arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDKs de avião de dados

Os SDKs do plano de dados permitem-lhe publicar eventos em tópicos, cuidando da autenticação, formando o evento e publicando assíncroticamente no ponto final especificado. Também lhe permitem consumir eventos de primeira festa. Atualmente, os seguintes SDKs estão disponíveis:

| Linguagem de programação | SDK | 
| -------------------- | ---------- | 
| .NET | Stable SDK: [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)<p>Pré-visualização SDK: [Azure.Messaging.EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/) |
| Java | SDK estável: [azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)<p>Preview SDK: [azure-messaging-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)</p> |  
| Python | [azure-eventgrid](https://pypi.org/project/azure-eventgrid/#history) (ver as versões mais recentes estáveis e pré-lançamento na página de histórico de **lançamento)** |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) (mude para o separador **Versões** para ver os mais recentes pacotes de versão estável e beta). | 
| Go | [SDK do Azure para Go](https://github.com/Azure/azure-sdk-for-go) |
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) |


## <a name="next-steps"></a>Passos seguintes

* Por exemplo, consulte as [amostras de código de Grade de Eventos](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Para uma introdução à Grade de Eventos, veja [o que é a Grade de Eventos?](overview.md)
* Para comandos de Grade de Eventos em Azure [CLI, consulte Azure CLI](/cli/azure/eventgrid).
* Para comandos de Grelha de Eventos em PowerShell, consulte [PowerShell](/powershell/module/az.eventgrid).
