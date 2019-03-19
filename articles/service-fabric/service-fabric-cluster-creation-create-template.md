---
title: Criar um modelo de cluster do Azure Service Fabric | Documentos da Microsoft
description: Saiba como criar um modelo do Resource Manager para um cluster do Service Fabric. Configure a segurança, o Azure Key Vault e o Azure Active Directory (Azure AD) para autenticação de cliente.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 8bfd6bd7e42d5978f6db2c2a6bdb634661c6bfed
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886750"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Criar um modelo de Gestor de recursos de cluster do Service Fabric

Uma [cluster do Azure Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais para o qual os microsserviços são implementados e geridos. Um cluster do Service Fabric em execução no Azure é um recurso do Azure e é implementado, geridos e monitorizados com o Resource Manager.  Este artigo descreve como criar um modelo do Resource Manager para um cluster do Service Fabric em execução no Azure.  Quando o modelo estiver concluído, pode [implementar o cluster no Azure](service-fabric-cluster-creation-via-arm.md).

Segurança do cluster é configurada quando o cluster, primeiro é configurado e não é possível alterar mais tarde. Antes de configurar um cluster, leia [cenários de segurança de cluster do Service Fabric][service-fabric-cluster-security]. No Azure, o Service Fabric utiliza x509 certificado para proteger o seu cluster e seus pontos de extremidade, autenticar clientes e encripta os dados. Também é recomendado proteger o acesso aos pontos finais de gestão do Azure Active Directory. O Azure AD inquilinos e os utilizadores devem ser criados antes de criar o cluster.  Para obter mais informações, leia [configurar o Azure AD para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

Antes de implementar um cluster de produção para executar cargas de trabalho de produção, certifique-se de que leia primeiro o [lista de verificação de preparação de produção](service-fabric-production-readiness-checklist.md).

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager
Modelos do Gestor de recursos de exemplo estão disponíveis no [exemplos do Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Estes modelos podem ser utilizados como ponto de partida para o modelo de cluster.

Este artigo utiliza a [cinco nós cluster seguro] [ service-fabric-secure-cluster-5-node-1-nodetype] modelo de exemplo e os parâmetros de modelo. Baixe *azuredeploy. JSON* e *azuredeploy* para o seu computador e abra ambos os ficheiros no seu editor de texto favorito.

> [!NOTE]
> Para nuvens nacionais (Azure Government, Azure China, Azure Alemanha), deverá ainda adicionar o seguinte procedimento `fabricSettings` ao seu modelo: `AADLoginEndpoint`, `AADTokenEndpointFormat` e `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Adicionar certificados
Adicionar certificados a um modelo do Resource Manager de cluster referenciando o Cofre de chaves que contém as chaves de certificado. Adicionar esses parâmetros de Cofre de chaves e valores de um ficheiro de parâmetros de modelo do Resource Manager (*azuredeploy*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Adicionar todos os certificados para o osProfile de conjunto de dimensionamento de máquina virtual
Cada certificado instalado no cluster tem de ser configurado no **osProfile** secção da escala definir recurso (Compute/virtualmachinescalesets). Esta ação instrui o fornecedor de recursos para instalar o certificado nas VMs. Esta instalação inclui o certificado de cluster e quaisquer certificados de segurança de aplicação que pretende utilizar nas suas aplicações:

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

### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurar o certificado de cluster do Service Fabric

O certificado de autenticação do cluster tem de ser configurado em ambas as o Service Fabric recurso de cluster (Microsoft.ServiceFabric/clusters) e a extensão do Service Fabric para o dimensionamento de máquinas virtuais define no recurso de conjunto de dimensionamento de máquina virtual. Esse esquema permite que o fornecedor de recursos do Service Fabric para configurá-lo para ser utilizado para autenticação de cluster e autenticação de servidor para pontos finais de gestão.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Adicionar que recurso de conjunto de informações do certificado de dimensionamento da Máquina Virtual

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

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Adicione as informações de certificado para o recurso de cluster do Service Fabric

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

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicionar configuração do Azure AD para utilizar o Azure AD para acesso de cliente

Adicionar a configuração do Azure AD para um modelo do Resource Manager de cluster referenciando o Cofre de chaves que contém as chaves de certificado. Adicione os parâmetros do Azure AD e os valores num ficheiro de parâmetros de modelo do Resource Manager (*azuredeploy*). 

> [!NOTE]
> O Azure AD inquilinos e os utilizadores devem ser criados antes de criar o cluster.  Para obter mais informações, leia [configurar o Azure AD para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

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

## <a name="populate-the-parameter-file-with-the-values"></a>Preencha o ficheiro de parâmetros com os valores

Por fim, utilize os valores de saída a partir do Cofre de chaves e os comandos do Azure AD PowerShell para povoar o ficheiro de parâmetros.

Se planeia utilizar os módulos do PowerShell do Azure service fabric RM, não é necessário preencher as informações do certificado de cluster. Se pretender que o sistema para gerar o self assinado certificado de segurança do cluster, apenas mantê-los como null. 

> [!NOTE]
> Para os módulos de RM recolher e preencher estes valores de parâmetro vazio, os nomes de parâmetros muito correspondem aos nomes abaixo

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

Se estiver a utilizar certificados de aplicação ou se estiver a utilizar um cluster existente que tenha carregado para o Cofre de chaves, terá de obter essas informações e preenchê-lo.

Os módulos do RM não têm a capacidade de gerar a configuração do Azure AD para, portanto, se planeia utilizar o Azure AD para acesso de cliente, precisa preenchê-lo.

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

## <a name="test-your-template"></a>Testar o seu modelo
Utilize o seguinte comando do PowerShell para testar o modelo do Resource Manager com um ficheiro de parâmetros:

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

No caso de problemas e obter mensagens enigmática, em seguida, utilize "-Debug" como uma opção.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

O diagrama seguinte ilustra onde o seu Cofre de chaves e a configuração do Azure AD encaixam em seu modelo do Resource Manager.

![Mapa de dependência de Gestor de recursos][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Passos Seguintes
Agora que tem um modelo para o seu cluster, saiba como [implementar o cluster para o Azure](service-fabric-cluster-creation-via-arm.md).  Se ainda não o tiver feito, leia os [lista de verificação de preparação de produção](service-fabric-production-readiness-checklist.md) antes de implementar um cluster de produção.

Para saber mais sobre a sintaxe JSON e propriedades para os recursos implementados neste artigo, consulte:

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
