---
title: Amostras Azure PowerShell - Obtenha detalhes do Azure Cloud Service (suporte alargado)
description: Scripts de amostra para recuperar informações sobre uma implantação dos Azure Cloud Services (suporte alargado)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 522ba95a5d6d97a8fde242274b1b0af969af20a5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881505"
---
# <a name="retrieve-information-about-your-azure-cloud-service-extended-support-deployments"></a>Recupere informações sobre as suas implementações do Azure Cloud Service (suporte alargado)

Estas amostras cobrem várias informações sobre as implementações existentes do Azure Cloud Service (suporte alargado).

## <a name="get-all-cloud-services-under-a-resource-group"></a>Obtenha todos os Serviços Cloud sob um grupo de recursos

```powershell
Get-AzCloudService -ResourceGroup "ContosOrg"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded
ContosOrg         ContosoCSTest     eastus2euap Failed
```

## <a name="get-single-cloud-service"></a>Obtenha um único Serviço cloud
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded

$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
$cloudService | Format-List
ResourceGroupName                       : ContosOrg
Configuration                           : <?xml version="1.0" encoding="utf-8"?>
                                          <ServiceConfiguration 
                                          xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
                                          </ServiceConfiguration>
ConfigurationUrl                        :
ExtensionProfileExtension               : {RDPExtension}
Id                                      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCS
Location                                : eastus2euap
Name                                    : ContosoCS
NetworkProfileLoadBalancerConfiguration : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
OSProfileSecret                         : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
PackageUrl                              : https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
ProvisioningState                       : Succeeded
RoleProfileRole                         : {ContosoFrontEnd, ContosoBackEnd}
StartCloudService                       :
SwappableCloudServiceId                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCSTest
Tag                                     : {
                                            "Owner": "Contos"
                                          }
Type                                    : Microsoft.Compute/cloudServices
UniqueId                                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
UpgradeMode                             : Auto
```

## <a name="get-cloud-service-instance-view"></a>Obtenha a visualização da instância do Serviço de Nuvem
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS" -InstanceView | Format-List

RoleInstanceStatusesSummary : {{
                                "code": "ProvisioningState/succeeded",
                                "count": 4
                              }, {
                                "code": "PowerState/started",
                                "count": 4
                              }}
Statuses                    : {{
                                "code": "ProvisioningState/succeeded",
                                "displayStatus": "Provisioning succeeded",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "PowerState/started",
                                "displayStatus": "Started",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "CurrentUpgradeDomain/-1",
                                "displayStatus": "Current Upgrade Domain of Cloud Service is -1.",
                                "level": "Info"
                              }, {
                                "code": "MaxUpgradeDomain/1",
                                "displayStatus": "Max Upgrade Domain of Cloud Service is 1.",
                                "level": "Info"
                              }}
```

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre os Serviços de Nuvem Azure (suporte alargado), consulte [a visão geral dos Serviços Azure Cloud (suporte alargado).](overview.md)
- Visite o [repositório](https://github.com/Azure-Samples/cloud-services-extended-support) de amostras cloud services (suporte alargado)