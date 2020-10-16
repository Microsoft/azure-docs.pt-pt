---
title: Dimensione um tipo de nó primário de tecido de serviço Azure
description: Aprenda a escalar um cluster de Tecido de Serviço adicionando um nó tipo.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: a18a40cc9e467b089ea9d6be3d0ca81a21d2c474
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89228720"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>Dimensione um tipo de nó primário de cluster de tecido de serviço adicionando um tipo de nó
Este artigo descreve como escalar um tipo de nó primário de cluster de tecido de serviço adicionando um tipo adicional de nó ao cluster. Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamada de nó. Os conjuntos de escala de máquinas virtuais são um recurso computacional Azure que utiliza para implementar e gerir uma coleção de máquinas virtuais como conjunto. Todos os tipos de nó definidos num cluster Azure [são configurado como um conjunto de escala separada](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode então ser gerido separadamente.

Os modelos de amostra no seguinte tutorial podem ser encontrados aqui: [Amostras de escalonamento do tipo de nó primário do tecido de serviço](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)

> [!WARNING]
> Não tente um procedimento de escala do tipo de nó primário se o estado do cluster não for saudável, pois isso só irá desestabilizar ainda mais o cluster.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>Processo para atualizar o tamanho e o sistema operativo do tipo de nó primário
Segue-se o processo de atualização do tamanho e sistema operativo VM dos VM do tipo de nó primário.  Após a atualização, os VMs do tipo de nó primário são o tamanho Standard D4_V2 e executar o Centro de Dados 2019 do Windows Server com contentores.

> [!WARNING]
> Antes de tentar este procedimento num cluster de produção, recomendamos que estude os modelos da amostra e verifique o processo contra um cluster de teste. O cluster também pode estar indisponível por um curto período de tempo. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>Implementar o cluster de tecido de serviço inicial 
Se quiser acompanhar a amostra, coloque o cluster inicial com um único nó primário e um conjunto de conjunto de escala [única Fabric - Cluster Inicial](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json). Pode saltar este passo se tiver um cluster de Tecido de Serviço já implantado. 

1. Inicie sessão na sua conta do Azure. 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. Criar um novo grupo de recursos. 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location
```
3. Preencha os valores dos parâmetros nos ficheiros do modelo. 
4. Implementar o cluster para o grupo de recursos criado no passo 2. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Adicione um novo tipo de nó primário ao cluster
> [!Note]
> Os recursos criados nos seguintes passos tornar-se-ão o novo tipo de nó primário no seu cluster uma vez que a operação de escala está completa. Certifique-se de que utiliza nomes exclusivos da sub-rede inicial, IP Público, Balanceador de Carga, Conjunto de Balanças de Máquina Virtual e Tipo de Nó. 

Pode encontrar um modelo com todos os seguintes passos concluídos aqui: [Tecido de Serviço - Novo Cluster tipo nó.](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json) Os passos seguintes contêm fragmentos de recursos parciais que destacam as mudanças nos novos recursos.  

1. Crie uma nova Sub-rede na sua Rede Virtual existente.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. Crie um novo recurso IP público com um domínio únicoNameLabel. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. Crie um novo recurso balanceador de carga que dependa do IP público acima criado. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. Crie um novo Conjunto de Balanças de Máquinas Virtuais que utilize os novos SKU VM e OS SKU que gostaria de escalar. 

Nó Tipo Ref 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

SKU da VM
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OS SKU 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

O seguinte corte é um exemplo de um novo recurso virtual de conjunto de escala de máquina que é usado para criar um novo tipo de nó para um cluster de Tecido de Serviço. Você gostaria de garantir que você inclui quaisquer extensões adicionais que são necessárias para a sua carga de trabalho. 

```json
    {
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeType1Name')]",
      "location": "[variables('computeLocation')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType1Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
      ],
      "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Automatic"
        },
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
              {
                "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType1Name'))]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "protectedSettings": {
                    "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                    "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                  },
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeType1Name')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "enableParallelJobs": true,
                    "nicPrefixOverride": "[variables('subnet1Prefix')]",
                    "certificate": {
                      "thumbprint": "[parameters('certificateThumbprint')]",
                      "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.0"
                }
              }
            ]
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[concat(variables('nicName'), '-1')]",
                "properties": {
                  "ipConfigurations": [
                    {
                      "name": "[concat(variables('nicName'),'-',1)]",
                      "properties": {
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[variables('lbPoolID1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[variables('lbNatPoolID1')]"
                          }
                        ],
                        "subnet": {
                          "id": "[variables('subnet1Ref')]"
                        }
                      }
                    }
                  ],
                  "primary": true
                }
              }
            ]
          },
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "computernamePrefix": "[variables('vmNodeType1Name')]",
            "secrets": [
              {
                "sourceVault": {
                  "id": "[parameters('sourceVaultValue')]"
                },
                "vaultCertificates": [
                  {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('certificateUrlValue')]"
                  }
                ]
              }
            ]
          },
          "storageProfile": {
            "imageReference": {
              "publisher": "[parameters('vmImagePublisher1')]",
              "offer": "[parameters('vmImageOffer1')]",
              "sku": "[parameters('vmImageSku1')]",
              "version": "[parameters('vmImageVersion1')]"
            },
            "osDisk": {
              "caching": "ReadOnly",
              "createOption": "FromImage",
              "managedDisk": {
                "storageAccountType": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      },
      "sku": {
        "name": "[parameters('vmNodeType1Size')]",
        "capacity": "[parameters('nt1InstanceCount')]",
        "tier": "Standard"
      },
      "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
      }
    },

```

5. Adicione um novo tipo de nó ao cluster, que faz referência ao Conjunto de Escala de Máquina Virtual que foi criado acima. A propriedade **éprimary** neste tipo de nó deve ser definida como verdadeira. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. Desloque o modelo ARM atualizado. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

O cluster de Tecido de Serviço terá agora dois tipos de nós quando a implementação estiver concluída. 

### <a name="remove-the-existing-node-type"></a>Remova o tipo de nó existente 
Uma vez que os recursos tenham terminado de ser implantados, pode começar a desativar os nós no tipo de nó primário original. À medida que os nós são desativados, os serviços do sistema migrarão para o novo tipo de nó primário que tinha sido implantado no degrau acima.

1. Desaprote a propriedade do tipo nó primário no recurso de cluster de tecido de serviço para falso. 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. Implemente o modelo com a propriedadeprimária atualizada no tipo de nó original. Pode encontrar um modelo com a bandeira primária definida como falsa no tipo de nó original aqui: [Tecido de Serviço - Tipo de Nó Primário Falso](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. Desative os nós no nó tipo 0. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* Para a durabilidade do bronze, aguarde que todos os nós entrem em estado de desativação.
* Para a durabilidade da prata e do ouro, alguns nós vão para deficientes e o resto estará em estado incapacitante. Verifique os detalhes do separador dos nós em estado incapacitante, se todos estiverem presos em garantir o quórum para as divisórias de serviço de infraestrutura, então é seguro continuar.

> [!Note]
> Este passo pode demorar um pouco a ser concluído. 

4. Pare os dados do nó tipo 0. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. Nódes deallocate no conjunto de escala de máquina virtual original 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> Os passos 6 e 7 são opcionais se já estiver a utilizar um IP público Standard SKU e um balanceador de carga Standard SKU. Neste caso, pode ter vários conjuntos de balanças de máquinas virtuais/tipos de nós sob o mesmo equilibrador de carga. 

6. Pode agora eliminar os recursos ip originais e de carregamento do Balancer. Neste passo também irá atualizar o nome DNS. 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. Atualize o ponto final de gestão no cluster para fazer referência ao novo IP. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. Retire o estado do nó do nó tipo 0.
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. Remova a referência original do tipo nó do conjunto de serviço no modelo ARM. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
Apenas para silver e clusters de durabilidade mais elevados, atualizar o recurso cluster no modelo e configurar políticas de saúde para ignorar a saúde da aplicação do tecido:/Sistema adicionando aplicaçõesDeltaHealthPolicies sob propriedades de recursos de cluster como indicado abaixo. A política abaixo deve ignorar os erros existentes, mas não permitir novos erros de saúde.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. Remova todos os outros recursos relacionados com o tipo de nó original do modelo ARM. Consulte [o Tecido de Serviço - Novo Cluster do Tipo de Nó](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) para um modelo com todos estes recursos originais removidos.

11. Implemente o modelo de Gestor de Recursos Azure modificado. ** Este passo vai demorar um pouco, normalmente até duas horas. Esta atualização alterará as definições para o Serviço de Infraestruturas; portanto, é necessário reiniciar um nó. Neste caso, o ForceRestart é ignorado. A atualização do parâmetroReplicaSetCheckTimeout especifica o tempo máximo que o Service Fabric espera que uma partição esteja num estado seguro, se não já em estado seguro. Uma vez que as verificações de segurança passam para todas as divisórias num nó, o Tecido de Serviço procede com a atualização desse nó. O valor para a atualização do parâmetroTimeout pode ser reduzido para 6 horas, mas para segurança máxima deve ser usado 12 horas.
Em seguida, valide que o recurso 'Service Fabric' no Portal mostra como pronto. 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

O tipo de nó primário do cluster foi agora atualizado. Verifique se as aplicações implementadas funcionam corretamente e que a saúde do cluster está bem.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [adicionar um nó ao aglomerado](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escalar um aglomerado Azure dentro ou fora](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) usando o fluente Azure compute SDK.
* [Escalar um aglomerado autónomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).
