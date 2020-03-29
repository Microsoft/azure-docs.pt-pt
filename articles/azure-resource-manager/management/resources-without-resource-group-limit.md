---
title: Recursos sem limite de contagem de 800
description: Lista os tipos de recursos Azure que podem ter mais de 800 instâncias num grupo de recursos.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 735cad0bfa936c41f603e42bdb9be77a1562cc1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937941"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Recursos não limitados a 800 instâncias por grupo de recursos

Por padrão, pode implementar até 800 instâncias de um tipo de recurso em cada grupo de recursos. No entanto, alguns tipos de recursos estão isentos do limite de 800 instâncias. Este artigo lista os tipos de recursos Azure que podem ter mais de 800 instâncias num grupo de recursos. Todos os outros tipos de recursos estão limitados a 800 instâncias.

Para alguns tipos de recursos, é necessário contactar o suporte para remover o limite de 800 instâncias. Estes tipos de recursos são anotados neste artigo.


## <a name="microsoftautomation"></a>Microsoft.Automação

* automatizaçõesContas

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registos
* registos/assinaturas de clientes
* registos/produtos
* verificaçãoKeys

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - Por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado contactando o apoio.

## <a name="microsoftcompute"></a>Microsoft.Compute

* discos
* imagens
* instantâneos
* máquinas virtuais

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* grupos de contentores

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registos/tarefas de construção
* registos/buildTasks/listSourceRepositoryProperties
* registos/construçãoTarefas/passos
* registos/buildTasks/steps/listBuildArguments
* registos/eventoSGridFilters
* registos/replicações
* registos/tarefas
* registos/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servidores

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servidores

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* servidorGrupos
* servidores
* servidorsv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* aglomerados
* espaços de nome

## <a name="microsoftexperimentation"></a>Microsoft.Experimentação

* experimentosEspaços workespaços

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoGeridoVmConfigurationPerfis
* configuraçãoPerfilAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftinsights"></a>Microsoft.Insights

* alertas métricos

## <a name="microsoftlogic"></a>Microsoft.Logic

* integraçõesContas
* fluxos de trabalho

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/instantâneos

## <a name="microsoftnetwork"></a>Microsoft.Network

* aplicaçãoGatewayWebApplicationFirewallPolicies
* aplicaçõesGrupos de Segurança
* bastionHosts
* ddosProtectionPlans
* zonas de dnszone
* zonas/A
* zonas/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* zonas de dnszone/todos
* dnszones/conjuntos de recordes
* políticas de intenções de rede
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresss - Por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado contactando o apoio.
* serviceEndpointPolicies
* perfis de gestor de tráfego
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootRecursos

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - Por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado contactando o apoio.

## <a name="microsoftrelay"></a>Microsoft.Relay

* espaços de nome

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* recolhas de emprego

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* espaços de nome

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* aplicações
* grupos de contentores
* gateways
* redes
* segredos
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* armazenamentoContas

## <a name="microsoftweb"></a>Microsoft.Web

* apiGeGeAccounts/apis
* sites

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista completa de quotas e limites, consulte os limites de subscrição e serviço do [Azure, quotas e restrições.](azure-subscription-service-limits.md)
