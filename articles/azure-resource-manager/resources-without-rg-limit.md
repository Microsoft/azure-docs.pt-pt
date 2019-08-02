---
title: Recursos do Azure sem limite de contagem de 800
description: Lista os tipos de recursos do Azure que podem ter mais de 800 instâncias em um grupo de recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/30/2019
ms.author: tomfitz
ms.openlocfilehash: a796896450a5b786e3b78aeddd81e6d66b02eb94
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700460"
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

* services

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* antivírus
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* fluxos

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

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista completa de cotas e limites, confira [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
