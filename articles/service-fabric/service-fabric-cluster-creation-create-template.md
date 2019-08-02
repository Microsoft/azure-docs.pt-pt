---
title: Criar um modelo de cluster de Service Fabric do Azure | Microsoft Docs
description: Saiba como criar um modelo do Resource Manager para um Cluster Service Fabric. Configure a segurança, Azure Key Vault e Azure Active Directory (Azure AD) para autenticação de cliente.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: atsenthi
ms.openlocfilehash: 9030a1d9d0b1e3f9b84f6636b0d3d758ab4cfa3b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599983"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Criar um modelo do Gerenciador de recursos de Cluster Service Fabric

Um [cluster de Service Fabric do Azure](service-fabric-deploy-anywhere.md) é um conjunto de máquinas virtuais conectadas à rede em que seus microserviços são implantados e gerenciados. Um Cluster Service Fabric em execução no Azure é um recurso do Azure e é implantado, gerenciado e monitorado usando o Gerenciador de recursos.  Este artigo descreve como criar um modelo do Resource Manager para um Cluster Service Fabric em execução no Azure.  Quando o modelo for concluído, você poderá [implantar o cluster no Azure](service-fabric-cluster-creation-via-arm.md).

A segurança do cluster é configurada quando o cluster é configurado pela primeira vez e não pode ser alterada posteriormente. Antes de configurar um cluster, leia [Service Fabric cenários de segurança de cluster][service-fabric-cluster-security]. No Azure, Service Fabric usa o certificado X509 para proteger o cluster e seus pontos de extremidade, autenticar clientes e criptografar dados. Azure Active Directory também é recomendável para proteger o acesso aos pontos de extremidade de gerenciamento. Os locatários e usuários do Azure AD devem ser criados antes da criação do cluster.  Para obter mais informações, leia [Configurar o Azure ad para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

Antes de implantar um cluster de produção para executar cargas de trabalho de produção, certifique-se de primeiro ler a [lista de verificação de preparação de produção](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager
Os modelos do Resource Manager de exemplo estão disponíveis nos [exemplos do Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Esses modelos podem ser usados como um ponto de partida para o modelo de cluster.

Este artigo usa o modelo de exemplo de [cluster seguro de cinco nós][service-fabric-secure-cluster-5-node-1-nodetype] e parâmetros de modelo. Baixe *azuredeploy. JSON* e *azuredeploy. Parameters. JSON* em seu computador e abra os dois arquivos em seu editor de texto favorito.

> [!NOTE]
> Para nuvens nacionais (Azure governamental, Azure China, Azure Alemanha), você também deve adicionar o seguinte `fabricSettings` ao seu modelo `AADTokenEndpointFormat` : `AADLoginEndpoint`e `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Adicionar certificados
Você adiciona certificados a um modelo do Resource Manager de cluster referenciando o cofre de chaves que contém as chaves de certificado. Adicione esses valores e parâmetros de cofre de chaves em um arquivo de parâmetros de modelo do Resource Manager (*azuredeploy. Parameters. JSON*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Adicionar todos os certificados ao conjunto de dimensionamento de máquinas virtuais osProfile
Todos os certificados instalados no cluster devem ser configurados na seção **osProfile** do recurso de conjunto de dimensionamento (Microsoft. Compute/virtualMachineScaleSets). Essa ação instrui o provedor de recursos a instalar o certificado nas VMs. Essa instalação inclui o certificado de cluster e quaisquer certificados de segurança de aplicativo que você planeja usar para seus aplicativos:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurar o certificado de Cluster Service Fabric

O certificado de autenticação de cluster deve ser configurado tanto no recurso de Cluster Service Fabric (Microsoft. Superfabric/clusters) quanto na extensão de Service Fabric para conjuntos de dimensionamento de máquinas virtuais no recurso do conjunto de dimensionamento de máquinas virtuais. Essa disposição permite que o provedor de recursos Service Fabric o configure para uso para autenticação de cluster e autenticação de servidor para pontos de extremidade de gerenciamento.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Adicionar as informações de certificado ao recurso de conjunto de dimensionamento de máquinas virtuais

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Adicionar as informações do certificado ao recurso de Cluster Service Fabric

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicionar a configuração do Azure AD para usar o Azure AD para acesso de cliente

Você adiciona a configuração do Azure AD a um modelo do Resource Manager de cluster referenciando o cofre de chaves que contém as chaves de certificado. Adicione esses parâmetros e valores do Azure AD em um arquivo de parâmetros de modelo do Resource Manager (*azuredeploy. Parameters. JSON*). 

> [!NOTE]
> Os locatários e usuários do Azure AD devem ser criados antes da criação do cluster.  Para obter mais informações, leia [Configurar o Azure ad para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Popular o arquivo de parâmetro com os valores

Por fim, use os valores de saída dos comandos do cofre de chaves e do PowerShell do Azure AD para preencher o arquivo de parâmetros.

Se você planeja usar os módulos do Azure Service Fabric RM PowerShell, não é necessário preencher as informações de certificado do cluster. Se você quiser que o sistema gere o certificado autoassinado para segurança de cluster, basta mantê-los como nulos. 

> [!NOTE]
> Para que os módulos do RM peguem e populem esses valores de parâmetro vazios, os nomes dos parâmetros correspondem aos nomes abaixo

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Se você estiver usando certificados de aplicativo ou estiver usando um cluster existente que você carregou no cofre de chaves, você precisará obter essas informações e preenchê-las.

Os módulos do RM não têm a capacidade de gerar a configuração do Azure AD para você, portanto, se você planeja usar o Azure AD para acesso de cliente, será necessário preenchê-lo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Testar seu modelo
Use o seguinte comando do PowerShell para testar seu modelo do Resource Manager com um arquivo de parâmetro:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Caso você tenha problemas e receba mensagens criptografadas, use "-debug" como uma opção.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

O diagrama a seguir ilustra onde o cofre de chaves e a configuração do Azure AD se ajustam ao seu modelo do Resource Manager.

![Mapa de dependência do Resource Manager][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Passos Seguintes
Agora que você tem um modelo para o cluster, saiba como [implantar o cluster no Azure](service-fabric-cluster-creation-via-arm.md).  Se você ainda não fez isso, leia a [lista de verificação de preparação de produção](service-fabric-production-readiness-checklist.md) antes de implantar um cluster de produção.

Para saber mais sobre a sintaxe e as propriedades JSON para os recursos implantados neste artigo, consulte:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
