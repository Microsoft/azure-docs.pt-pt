---
title: Configurar os modos de funcionamento em rede para serviços de contentor do Azure Service Fabric | Documentos da Microsoft
description: Saiba como configurar os diferentes modos de funcionamento em rede que são suportados pelo Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: 01b1cfafab75acef918b001752837a4dc44ca909
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57899048"
---
# <a name="service-fabric-container-networking-modes"></a>Modos de funcionamento em rede de contentor do Service Fabric

Um cluster do Azure Service Fabric para o contentor dos serviços de usos **nat** modo de funcionamento em rede por predefinição. Quando mais de um serviço de contentor está à escuta na mesma porta e modo de nat está a ser utilizado, podem ocorrer erros de implementação. Para oferecer suporte a vários serviços de contentor escuta na mesma porta, oferece o Service Fabric **aberto** modo de funcionamento em rede (versões 5.7 e posteriores). Abra o modo, cada serviço de contentor tem um interno dinamicamente atribuído o endereço IP que oferece suporte a vários serviços de escuta na mesma porta.  

Se tiver um serviço de contentor com um ponto de final estático no seu manifesto de serviço, pode criar e eliminar os novos serviços com o modo aberto sem erros de implementação. Também pode ser utilizado o mesmo ficheiro docker-Compose com mapeamentos de porta estática para criar vários serviços.

Quando um serviço de contentor é reiniciado ou se move para outro nó no cluster, o endereço IP é alterado. Por esse motivo, não recomendamos utilizar o endereço IP atribuído dinamicamente para detetar os serviços de contentores. Apenas o serviço Service Fabric Naming ou o serviço DNS deve ser utilizado para a deteção de serviço. 

>[!WARNING]
>Azure permite que um total de 65,356 IPs por rede virtual. A soma do número de nós e o número de instâncias de serviço de contentor (que estão a utilizar o modo de abrir) não pode exceder os 65,356 IPs dentro de uma rede virtual. Para cenários de alta densidade, recomendamos o modo de funcionamento em rede nat. Além disso, outras dependências, tais como o Balanceador de carga terá outro [limitações](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits) a serem considerados. Atualmente até 50 IPs por nó foram testadas e comprovadas estável. 
>

## <a name="set-up-open-networking-mode"></a>Configurar a abrir o modo de funcionamento em rede

1. Configure o modelo Azure Resource Manager. Na **fabricSettings** seção de recurso do Cluster, ativar o serviço de DNS e o fornecedor de IP: 

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
    
2. Configure a seção de perfil de rede do recurso de conjunto de dimensionamento de Máquina Virtual. Isso permite que vários endereços IP a ser configurado em cada nó do cluster. O exemplo seguinte define cinco endereços IP por nó para um cluster do Service Fabric do Windows/Linux. Pode ter cinco instâncias do serviço de escuta na porta em cada nó. Para que os IPs de cinco esteja acessível a partir do Balanceador de carga do Azure, inscreva os cinco IPs no conjunto de endereços de back-end de Balanceador de carga do Azure, conforme mostrado abaixo.  Também terá de adicionar as variáveis na parte superior do seu modelo na secção de variáveis.

    Adicione esta secção para variáveis:

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
    
    Adicione esta secção para o recurso de conjunto de dimensionamento de Máquina Virtual:

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
 
3. Para os clusters do Windows apenas, configure uma regra de grupo de segurança de rede (NSG) do Azure que se abre a porta 53 de UDP/para a rede virtual com os seguintes valores:

   |Definição |Value | |
   | --- | --- | --- |
   |Prioridade |2000 | |
   |Name |Custom_Dns  | |
   |Origem |VirtualNetwork | |
   |Destino | VirtualNetwork | |
   |Serviço | DNS (UDP/53) | |
   |Ação | Permitir  | |
   | | |

4. Especificar o modo de funcionamento em rede no manifesto do aplicativo para cada serviço: `<NetworkConfig NetworkType="Open">`. **Abra** resultados de modo no serviço de obter um endereço IP dedicado de rede. Se não for especificado um modo, o serviço assume a predefinição **nat** modo. No exemplo a seguir manifesto, o `NodeContainerServicePackage1` e `NodeContainerServicePackage2` services podem cada escuta na mesma porta (ambos os serviços estão à escuta na `Endpoint1`). Quando abrir o modo de funcionamento em rede é especificado, `PortBinding` não não possível especificar configurações.

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

    Pode misturar e combinar diferentes modos de funcionamento em rede em todos os serviços dentro de um aplicativo para um cluster do Windows. Alguns serviços podem utilizar o modo aberto, enquanto outros utilizam o modo de nat. Quando um serviço está configurado para utilizar o modo de nat, a porta que está a escutar o serviço tem de ser exclusiva.

    >[!NOTE]
    >Em clusters do Linux, misturar os modos de funcionamento em rede para serviços diferentes não é suportada. 
    >

5. Quando o **aberto** modo está selecionado, o **Endpoint** definição no manifesto do serviço explicitamente deve apontar para o pacote de código correspondente para o ponto final, mesmo que o pacote de serviço tem apenas um código pacote no mesmo. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Para Windows, um reinício VM fará com que a rede aberta de ser recriados. Isso é reduzir um problema subjacente na pilha da rede. O comportamento padrão é recriar a rede. Se esse comportamento tem de ser desativada, pode ser utilizada a seguinte configuração seguido de uma atualização de configuração.

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
* [Saiba mais sobre os recursos de manifestos do serviço Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Implementar um contentor do Windows no Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implementar um contentor de Docker para o Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
