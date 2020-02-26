---
title: Serviços de mensagens Azure - Gestor de Serviços para Gestor de Recursos
description: Este artigo fornece mapeamento de cmdlets de gerente de serviço azul preccated REST API & PowerShell para o Gestor de Recursos REST API & PowerShell cmdlets.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589912"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Deprecation do apoio do Gestor de Serviços Azure para ônibus de serviço, relé e hubs de eventos

O Gestor de Recursos, a nossa pilha de infraestruturas em nuvem de próxima geração, está a substituir totalmente o modelo "clássico" de Gestão de Serviços Azure (modelo clássico de implantação). Como resultado, o modelo clássico de implantação REST APIs e o suporte para Service Bus, Relay e Event Hubs serão retirados a 1 de novembro de 2021. Esta depreciação foi anunciada pela primeira vez num anúncio da [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909), mas decidimos recentemente prolongar o período de depreciação por mais dois anos a contar do momento do anúncio original. Para facilitar a identificação, estas APIs têm `management.core.windows.net` no seu URI. Consulte a tabela seguinte para obter uma lista das APIs depreciadas e da sua versão API do Gestor de Recursos Azure que deve agora utilizar.

Para continuar a utilizar service bus, relay e Event Hubs, mude-se para Resource Manager até 31 de outubro de 2021. Encorajamos todos os clientes que ainda estão a usar APIs antigos para fazer a mudança em breve para tirar partido dos benefícios adicionais do Gestor de Recursos, que incluem agrupamento de recursos, tags, um processo de implantação e gestão simplificado, e acesso fino controlo utilizando o controlo de acesso baseado em funções (RBAC).

Para obter mais informações sobre o Gestor de Recursos Azure vs Azure Service Manager, consulte o [TechNet Blog](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/).

Para obter mais informações sobre o Gestor de Serviços e o Gestor de Recursos APIs para o Azure Service Bus, Relay e Event Hubs, consulte a nossa documentação REST API:

- [Azure Service Bus](/rest/api/servicebus/)
- [Azure Event Hubs](/rest/api/eventhub/)
- [Relé Azure](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Gestor de Serviços REST API - Gestor de Recursos REST API

| APIs do Gestor de Serviços (Deprecated) | Gestor de Recursos - Service Bus API | Gestor de Recursos - Event Hub API | Gestor de Recursos - Relay API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Espaços de Nome-GetNamespaceAsync** <br/>[Ônibus de serviço obter espaço de nome](/rest/api/servicebus/get-namespace)<br/>[Centro de eventos Obter Espaço de Nome](/rest/api/eventhub/get-event-hub)<br/>[Retransmissor Obter Espaço de Nome](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [Obter](/rest/api/servicebus/namespaces/get) | [Obter](/rest/api/eventhub/namespaces/get) | [Obter](/rest/api/relay/namespaces/get) |
| **Detalhes de ligação-ObterDetalhes**<br/>Ônibus de serviço/hub de evento/retransmissão GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/namespaces/listkeys) | [listkeys](/rest/api/eventhub/namespaces/listkeys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **Tópicos-GetTopicsAsync**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Filas-GetQueueAsync** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [Obter](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Retransmissores-GetRelaysAsync**<br/>[Obter Relés](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NomespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>Ônibus de serviço/centro de eventos/relay GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [obter regras de autorização](/rest/api/servicebus/namespaces/getauthorizationrule) |[obter regras de autorização](/rest/api/eventhub/namespaces/getauthorizationrule) | [obter regras de autorização](/rest/api/relay/namespaces/getauthorizationrule) |
| **Espaços de Nome-DeleteNamespaceAsync**<br/>[Service Bus Eliminar Espaço de Nome](/rest/api/servicebus/delete-namespace)<br/>[Hubs de eventos apagam espaço de nome](/rest/api/eventhub/delete-event-hub)<br/>[Retransmissores apagam espaço de nome](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [eliminar](/rest/api/servicebus/namespaces/delete) | [eliminar](/rest/api/eventhub/namespaces/delete) | [eliminar](/rest/api/relay/namespaces/delete) | 
| **MensagensSKUPlan-GetPlanAsync**<br/>Ônibus de serviço/centro de eventos/retransmissão obter espaço de nome<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [Obter](/rest/api/servicebus/namespaces/get) | [Obter](/rest/api/eventhub/namespaces/get) | [Obter](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>Ônibus de serviço/centro de eventos/retransmissão obter espaço de nome<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NomespaceAuthorizationRules-UpdatenameNameAuthorizationRuleAsync**<br/>Ônibus de serviço/centro de eventos/retransmissão obter espaço de nome<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [regra de autorização de atualização](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [regra de autorização de atualização](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NomespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
Ônibus de serviço/centro de eventos/retransmissão<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [regra de autorização de atualização](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [regra de autorização de atualização](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[Ônibus de serviço obter espaço de nome](/rest/api/servicebus/get-namespace)<br/>[Hubs de eventos obtêm espaço de nome](/rest/api/eventhub/get-event-hub)<br/>[Retransmissor Obter Espaço de Nome](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [Obter](/rest/api/servicebus/namespaces/get) | [Obter](/rest/api/eventhub/namespaces/get) | [Obter](/rest/api/relay/namespaces/get) |
| **RegiõesCódigos-GetRegionCodesAsync**<br/>Ônibus de serviço/EventHub/Relay Obter Espaço nome<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | [listbysku](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>Ônibus de serviço/Eventhub/Relay<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[List Event Hubs](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Obter Hubs de Eventos](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [Obter](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **NomespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>Ônibus de serviço/centro de eventos/retransmissão<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [excluir regras de autorização](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [excluir regras de autorização](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [excluir regras de autorização](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NomespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>Ônibus de serviço/Eventhub/Relay<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [regras de autorização de listas](/rest/api/servicebus/namespaces/listauthorizationrules) | [regras de autorização de listas](/rest/api/eventhub/namespaces/listauthorizationrules) | [regras de autorização de listas](/rest/api/relay/namespaces/listauthorizationrules) |
| **Espaço de nomeDisponibilidade-IsNamespaceDisponível**<br/>[Disponibilidade de espaço de nome de ônibus de serviço](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknamedisponibilidade](/rest/api/servicebus/namespaces/checknameavailability) | [checknamedisponibilidade](/rest/api/eventhub/namespaces/checknameavailability) | [checknamedisponibilidade](/rest/api/relay/namespaces/checknameavailability) |
| **Espaços de Nome-CreateOrUpdateNameSpaceAsync**<br/>Ônibus de serviço/centro de eventos/retransmissão<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Tópicos-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [Obter](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Gestor de ServiçoS PowerShell - Gestor de Recursos PowerShell
| Comando PowerShell gestor de serviço (Deprecated) | Comandos de Novo Gestor de Recursos | Comando de Gestor de Recursos Mais Recente |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusGeoDRConfiguração](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Get-AzServiceBusGeoDRConfiguração](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Espaço New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [Espaço New-AzServiceBusName](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Remover-AzureRmRelayAuthorizationRule](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remover-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Remover-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remover-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remover-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Remover-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte documentação: 

- Última documentação da API REST
    - [Azure Service Bus](/rest/api/servicebus/)
    - [Azure Event Hubs](/rest/api/eventhub/)
    - [Relé Azure](/rest/api/relay/)
- Documentação mais recente da PowerShell
    - [Azure Service Bus](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure Event Hubs](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
