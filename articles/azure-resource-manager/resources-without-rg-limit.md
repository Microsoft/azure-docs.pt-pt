---
title: Recursos do Azure sem limite de contagem de 800
description: Lista os tipos de recursos do Azure que podem ter mais de 800 instâncias em um grupo de recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/4/2019
ms.author: tomfitz
ms.openlocfilehash: 6677290999049dead40f39e9a840735810c7c763
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973312"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Recursos não limitados a 800 instâncias por grupo de recursos

Por padrão, você pode implantar até 800 instâncias de um tipo de recurso em cada grupo de recursos. No entanto, alguns tipos de recursos são isentos do limite da instância 800. Este artigo lista os tipos de recursos do Azure que podem ter mais de 800 instâncias em um grupo de recursos. Todos os outros tipos de recursos são limitados a 800 instâncias.

Para alguns tipos de recursos, você precisa entrar em contato com o suporte para que o limite da instância 800 seja removido. Esses tipos de recursos são indicados neste artigo.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registros
* registrations/customerSubscriptions
* registros/produtos

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices-contatar o suporte para estender o limite.

## <a name="microsoftcompute"></a>Microsoft.Compute

* discos
* imagens
* instantâneos
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registros/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* registros/buildTasks/etapas/listBuildArguments
* registries/eventGridFilters
* registros/replicações
* registros/tarefas
* registros/WebHooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servidores

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servidores

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* servidores
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* serviços

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* antivírus
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* fluxos

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/instantâneos

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
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
* dnszones/tudo
* dnszones/recordsets
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
* privateDnsZones/tudo
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses-contatar o suporte para estender o limite.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections-contatar o suporte para estender o limite.

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* gratuitas

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* aplicações
* containerGroups
* gateways
* às
* segredos
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista completa de cotas e limites, confira [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
