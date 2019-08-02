---
title: Configurar modos de rede para os serviços de contêiner de Service Fabric do Azure | Microsoft Docs
description: Saiba como configurar os diferentes modos de rede com suporte do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: d749e1355e69ad93c8c211474043f88127ec76f0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599383"
---
# <a name="service-fabric-container-networking-modes"></a>Modos de rede de contêineres Service Fabric

Um Cluster Service Fabric do Azure para serviços de contêiner usa o modo de rede **NAT** por padrão. Quando mais de um serviço de contêiner está escutando na mesma porta e o modo NAT está sendo usado, podem ocorrer erros de implantação. Para dar suporte a vários serviços de contêiner escutando na mesma porta, o Service Fabric oferece o modo de rede **aberta** (versões 5,7 e posteriores). No modo aberto, cada serviço de contêiner tem um endereço IP interno e atribuído dinamicamente que dá suporte a vários serviços escutando na mesma porta.  

Se você tiver um serviço de contêiner com um ponto de extremidade estático no manifesto do serviço, poderá criar e excluir novos serviços usando o modo aberto sem erros de implantação. O mesmo arquivo Docker-Compose. yml também pode ser usado com mapeamentos de porta estáticos para criar vários serviços.

Quando um serviço de contêiner é reiniciado ou movido para outro nó no cluster, o endereço IP é alterado. Por esse motivo, não recomendamos o uso do endereço IP atribuído dinamicamente para descobrir os serviços de contêiner. Somente o Serviço de Nomenclatura Service Fabric ou o serviço DNS deve ser usado para a descoberta de serviço. 

>[!WARNING]
>O Azure permite um total de 65.356 IPs por rede virtual. A soma do número de nós e o número de instâncias de serviço de contêiner (que estão usando o modo aberto) não podem exceder 65.356 IPs em uma rede virtual. Para cenários de alta densidade, recomendamos o modo de rede NAT. Além disso, outras dependências, como o balanceador de carga, terão outras [limitações](https://docs.microsoft.com/azure/azure-subscription-service-limits) a serem consideradas. Atualmente, até 50 IPs por nó foram testados e são estáveis em termos de estabilidade. 
>

## <a name="set-up-open-networking-mode"></a>Configurar o modo de rede aberto

1. Configure o modelo de Azure Resource Manager. Na seção **fabricSettings** do recurso de cluster, habilite o serviço DNS e o provedor de IP: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Configurar a seção de perfil de rede do recurso de conjunto de dimensionamento de máquinas virtuais. Isso permite que vários endereços IP sejam configurados em cada nó do cluster. O exemplo a seguir configura cinco endereços IP por nó para um Cluster Service Fabric do Windows/Linux. Você pode ter cinco instâncias de serviço ouvindo na porta em cada nó. Para que os cinco IPs possam ser acessados do Azure Load Balancer, registre os cinco IPs no pool de endereços de back-end Azure Load Balancer, conforme mostrado abaixo.  Você também precisará adicionar as variáveis à parte superior do modelo na seção variáveis.

    Adicione esta seção às variáveis:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Adicione esta seção ao recurso do conjunto de dimensionamento de máquinas virtuais:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. Somente para clusters do Windows, configure uma regra de NSG (grupo de segurança de rede) do Azure que abre a porta UDP/53 para a rede virtual com os seguintes valores:

   |Definição |Value | |
   | --- | --- | --- |
   |Priority |2000 | |
   |Nome |Custom_Dns  | |
   |Source |VirtualNetwork | |
   |Destino | VirtualNetwork | |
   |Serviço | DNS (UDP/53) | |
   |Action | Allow  | |
   | | |

4. Especifique o modo de rede no manifesto do aplicativo para cada serviço `<NetworkConfig NetworkType="Open">`:. O modo de rede **aberto** resulta no serviço que obtém um endereço IP dedicado. Se um modo não for especificado, o serviço usa como padrão o modo **NAT** . No exemplo de manifesto a seguir, `NodeContainerServicePackage1` os `NodeContainerServicePackage2` serviços e podem escutar na mesma porta ( `Endpoint1`ambos os serviços estão escutando). Quando o modo de rede aberto é `PortBinding` especificado, as configurações não podem ser especificadas.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Você pode misturar e combinar diferentes modos de rede entre serviços em um aplicativo para um cluster do Windows. Alguns serviços podem usar o modo aberto enquanto outros usam o modo NAT. Quando um serviço é configurado para usar o modo NAT, a porta em que o serviço está escutando deve ser exclusiva.

    >[!NOTE]
    >Em clusters do Linux, não há suporte para a combinação de modos de rede para serviços diferentes. 
    >

5. Quando o modo **aberto** é selecionado, a definição do **ponto de extremidade** no manifesto do serviço deve apontar explicitamente para o pacote de códigos correspondente ao ponto de extremidade, mesmo que o pacote de serviço tenha apenas um pacote de código. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Para o Windows, uma reinicialização de VM fará com que a rede aberta seja recriada. Isso é para atenuar um problema subjacente na pilha de rede. O comportamento padrão é recriar a rede. Se esse comportamento precisar ser desativado, a configuração a seguir poderá ser usada seguida por uma atualização de configuração.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Passos Seguintes
* [Understand the Service Fabric application model (Compreender o modelo de aplicações do Service Fabric)](service-fabric-application-model.md)
* [Saiba mais sobre os recursos de manifesto do serviço Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Implantar um contêiner do Windows para Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
