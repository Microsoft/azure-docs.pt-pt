---
title: Crie um modelo de cluster de tecido de serviço Azure
description: Saiba como criar um modelo de Gestor de Recursos para um cluster de Tecido de Serviço. Configure segurança, Azure Key Vault e Azure Ative Directory (Azure AD) para a autenticação do cliente.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: b028b5c1f32733146c071e50b24202e5bad945c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260487"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Crie um modelo de gestor de recursos de cluster de tecido de serviço

Um [cluster Azure Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas virtuais ligadas à rede em que os seus microserviços são implantados e geridos. Um cluster de tecido de serviço em execução em Azure é um recurso Azure e é implantado, gerido e monitorizado usando o Gestor de Recursos.  Este artigo descreve como criar um modelo de Gestor de Recursos para um cluster de tecido de serviço em execução em Azure.  Quando o modelo estiver completo, pode [implantar o cluster no Azure](service-fabric-cluster-creation-via-arm.md).

A segurança do cluster é configurada quando o cluster é configurado pela primeira vez e não pode ser alterado mais tarde. Antes de configurar um cluster, leia [os cenários de segurança do cluster de tecido de serviço][service-fabric-cluster-security]. No Azure, a Service Fabric utiliza o certificado x509 para proteger o seu cluster e os seus pontos finais, autenticar clientes e encriptar dados. O Azure Ative Directory também é recomendado para garantir o acesso aos pontos finais de gestão. Os inquilinos e utilizadores da AD Azure devem ser criados antes de criar o cluster.  Para mais informações, leia [Configurar AD AZure para autenticar clientes.](service-fabric-cluster-creation-setup-aad.md)

Antes de implantar um cluster de produção para executar cargas de trabalho de produção, leia primeiro a [lista de verificação de prontidão](service-fabric-production-readiness-checklist.md)de produção .


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager
Os modelos do Gestor de Recursos da Amostra estão disponíveis nas [amostras do Azure no GitHub.](https://github.com/Azure-Samples/service-fabric-cluster-templates) Estes modelos podem ser usados como ponto de partida para o seu modelo de cluster.

Este artigo utiliza o modelo de exemplo [de cluster seguro de cinco nós][service-fabric-secure-cluster-5-node-1-nodetype] e os parâmetros do modelo. Faça * o downloadazuredeploy.js* eazuredeploy.parameters.js* no* seu computador e abra ambos os ficheiros no seu editor de texto favorito.

> [!NOTE]
> Para as nuvens nacionais (Governo Azure, Azure China, Azure Germany), também deve adicionar o seguinte `fabricSettings` ao seu modelo: `AADLoginEndpoint` e `AADTokenEndpointFormat` `AADCertEndpointFormat` .

## <a name="add-certificates"></a>Adicionar certificados
Você adiciona certificados a um modelo de Gestor de Recursos de cluster, referindo-se ao cofre de chaves que contém as teclas de certificado. Adicione esses parâmetros e valores de cofre-chave num ficheiro de parâmetros do gestor de recursos* (azuredeploy.parameters.js).*

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Adicione todos os certificados à escala de máquina virtual conjunto osProfile
Todos os certificados instalados no cluster devem ser configurados na secção **osProfile** do recurso conjunto de escala (Microsoft.Compute/virtualMachineScaleSets). Esta ação instrui o fornecedor de recursos a instalar o certificado nos VMs. Esta instalação inclui tanto o certificado de cluster como quaisquer certificados de segurança de aplicação que planeie utilizar para as suas aplicações:

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

### <a name="configure-the-service-fabric-cluster-certificate"></a>Configure o certificado de cluster de tecido de serviço

O certificado de autenticação do cluster deve ser configurado tanto no recurso de cluster de tecido de serviço (Microsoft.ServiceFabric/clusters) como na extensão do Tecido de Serviço para conjuntos de escala de máquina virtual no recurso conjunto de escala de máquina virtual. Este arranjo permite ao fornecedor de recursos do Service Fabric configugá-lo para utilização para autenticação de clusters e autenticação do servidor para pontos finais de gestão.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Adicione a informação do certificado o recurso conjunto de escala de máquina virtual

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
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
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

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Adicione as informações do certificado ao recurso cluster De Tecido de Serviço

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

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicione a configuração AD AZure para usar Azure AD para acesso ao cliente

Adicione a configuração AD AD do Azure a um modelo de Gestor de Recursos de cluster, fazendo referência ao cofre de chaves que contém as teclas de certificado. Adicione os parâmetros e valores AD do Azure num ficheiro de parâmetros do gestor de recursos* (azuredeploy.parameters.jsem*). 

> [!NOTE]
> Em Linux, os inquilinos e utilizadores da AD Azure devem ser criados antes de criar o cluster.  Para mais informações, leia [Configurar AD AZure para autenticar clientes.](service-fabric-cluster-creation-setup-aad.md)

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

## <a name="populate-the-parameter-file-with-the-values"></a>Povoar o arquivo de parâmetros com os valores

Finalmente, utilize os valores de saída do cofre-chave e os comandos Azure AD PowerShell para preencher o ficheiro de parâmetros.

Se planeia utilizar os módulos RM PowerShell do tecido de serviço Azure, então não precisa de preencher as informações do certificado de cluster. Se quer que o sistema gere o certificado auto-assinado para a segurança do cluster, mantenha-os como nulos. 

> [!NOTE]
> Para que os módulos RM recolham e povoem estes valores de parâmetros vazios, os nomes dos parâmetros correspondem muito aos nomes abaixo

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

Se estiver a utilizar certificados de aplicação ou estiver a utilizar um cluster existente que carregou para o cofre da chave, precisa de obter estas informações e povoá-la.

Os módulos RM não têm a capacidade de gerar a configuração AD Azure para si, por isso, se planeia utilizar o AD Azure para acesso ao cliente, precisa de o povoar.

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

## <a name="test-your-template"></a>Teste o seu modelo
Utilize o seguinte comando PowerShell para testar o seu modelo de Gestor de Recursos com um ficheiro de parâmetro:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Caso se desperdique a problemas e obtenha mensagens enigmáticas, use o "Debug" como opção.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

O diagrama que se segue ilustra onde o cofre da chave e a configuração AD do Azure se encaixam no seu modelo de Gestor de Recursos.

![Mapa de dependência do gestor de recursos][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Passos seguintes
Agora que tem um modelo para o seu cluster, aprenda a [implantar o cluster para Azure.](service-fabric-cluster-creation-via-arm.md)  Se ainda não o fez, leia a [lista de verificação de prontidão](service-fabric-production-readiness-checklist.md) de produção antes de implantar um cluster de produção.

Para conhecer a sintaxe JSON e as propriedades para os recursos implantados neste artigo, consulte:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAcontas](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: ../azure-resource-manager/templates/deploy-powershell.md

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
