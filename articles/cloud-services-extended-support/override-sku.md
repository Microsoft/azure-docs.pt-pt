---
title: Sobrepor informações SKU sobre CSCFG/CSDEF para serviços em nuvem Azure (suporte alargado)
description: Sobrepor informações SKU sobre CSCFG/CSDEF para serviços em nuvem Azure (suporte alargado)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: 17e47b562c52ffce631a01cf03004d77053ea647
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387335"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Sobrepor informações SKU sobre CSCFG/CSDEF em Serviços de Nuvem (suporte alargado) 

Esta funcionalidade permitirá ao utilizador atualizar o tamanho da função e a contagem de instâncias no seu Serviço cloud utilizando a propriedade **allowModelOverride** sem ter de atualizar os ficheiros de configuração de serviço e definição de serviço, permitindo assim que o serviço de nuvem se dimensione para cima/para baixo/para fora sem fazer uma reembalagem e redistribuição.

## <a name="set-allowmodeloverride-property"></a>Definir permitir Propriedade DeModelOverride
A propriedade allowModelOverride pode ser definida das seguintes formas:
* Quando permitir o MadelOverride = verdadeiro, a chamada API atualizará o tamanho da função e a contagem de exemplos para o serviço na nuvem sem validar os valores com os ficheiros csdef e cscfg. 
> [!Note]
> O cscfg será atualizado para refletir a contagem de exemplos de papel, mas o csdef (dentro do cspkg) irá manter os valores antigos
* Quando permite o MadelOverride = falso, a chamada da API lançaria um erro quando o tamanho da função e os valores de contagem de instâncias não correspondem aos ficheiros csdef e cscfg respectivamente

O valor predefinido é definido como falso. Se a propriedade for reposta para falsa de volta de verdade, os ficheiros csdef e cscfg seriam novamente verificados para validação.

Por favor, consulte as amostras abaixo para aplicar a propriedade em PowerShell, modelo e SDK

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Definir a propriedade "allowModelOverride" = verdadeiro aqui irá atualizar o serviço de nuvem com as propriedades de função definidas na secção roleProfile
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “**allowModelOverride**” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
Definir o interruptor "AllowModelOverride" no novo cmdlet New-AzCloudService, atualizará o serviço de nuvem com as propriedades SKU definidas no RoleProfile
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
Definir a variável AllowModelOverride= verdadeiro irá atualizar o serviço de nuvem com as propriedades SKU definidas no RoleProfile

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Portal do Azure
O portal não permite que a propriedade acima sobrepõe o tamanho da função e a contagem de instâncias no CSDEF e cscfg. 


## <a name="next-steps"></a>Passos seguintes 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
