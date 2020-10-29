---
title: Recursos sem limite de contagem de 800
description: Lista os tipos de recursos Azure que podem ter mais de 800 instâncias num grupo de recursos.
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: cec7ea87bbe81d3bfcca37f3133ac650e66f849f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913505"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Recursos não limitados a 800 instâncias por grupo de recursos

Por predefinição, pode implementar até 800 instâncias de um tipo de recurso em cada grupo de recursos. No entanto, alguns tipos de recursos estão isentos do limite de 800 instâncias. Este artigo lista os tipos de recursos Azure que podem ter mais de 800 instâncias num grupo de recursos. Todos os outros tipos de recursos estão limitados a 800 instâncias.

Para alguns tipos de recursos, é necessário contactar o suporte para que o limite de 800 instâncias seja removido. Estes tipos de recursos são anotados neste artigo.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automação Contas

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* edgeSubscriptions
* linkedSubscriptions
* registos
* registos/clienteSubscrições
* registos/produtos

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - Por padrão, limitado a 800 instâncias. Este limite pode ser aumentado através do contacto com o suporte.

## <a name="microsoftcompute"></a>Microsoft.Compute

* discos
* galerias
* galerias/imagens
* galerias/imagens/versões
* imagens
* instantâneos
* virtualMachineScaleSets - Por padrão, limitado a 800 instâncias. Este limite pode ser aumentado através do contacto com o suporte.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* grupos de contentores

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registos/buildTasks
* registos/buildTasks/listSourceRepositoryProperties
* registos/buildTasks/passos
* registos/buildTasks/steps/listBuildArguments
* registos/eventGridFilters
* registos/replicações
* registos/tarefas
* registos/webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

* casos

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servidores

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* servidores

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

* flexibleServers
* grupos de servidores
* servidores
* servidorv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* laboratórios/virtualMachines - Por padrão, limitado a 800 casos. Este limite pode ser aumentado através do contacto com o suporte.
* horários

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* aglomerados
* espaços de nome

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentOs espaços de trabalho

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configuraçãoProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integraçãoAconselhos
* fluxos de trabalho

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots

## <a name="microsoftnetwork"></a>Microsoft.Network

* aplicaçãoGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/todos
* dnszones/recordets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privadosEndpoints
* privateLinkServices
* publicIPAddresses - Por defeito, limitado a 800 casos. Este limite pode ser aumentado através do contacto com o suporte.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - Por padrão, limitado a 800 instâncias. Este limite pode ser aumentado através do contacto com o suporte.

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* capacidades - Por defeito, limitada a 800 instâncias. Este limite pode ser aumentado através do contacto com o suporte.

## <a name="microsoftrelay"></a>Microsoft.Relay

* espaços de nome

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* trabalhocollectões

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* espaços de nome

## <a name="microsoftsingularity"></a>Microsoft.Singularity

* accounts
* contas/contaQuotaPolicies
* contas/grupoPolícias
* contas/postos de trabalho
* contas/armazenamentoContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* armazenamento Contas

## <a name="microsoftsql"></a>Microsoft.Sql

* servidores/bases de dados

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista completa de quotas e limites, consulte [os limites de subscrição e serviços, quotas e restrições da Azure.](azure-subscription-service-limits.md)
