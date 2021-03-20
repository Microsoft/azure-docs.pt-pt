---
title: Configurar modos de rede para serviços de contentores
description: Saiba como configurar os diferentes modos de rede que são suportados pela Azure Service Fabric.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: e6174f35bd54b3ca0b2c5240a663369350b30ce8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86241901"
---
# <a name="service-fabric-container-networking-modes"></a>Modos de rede de contentores de tecido de serviço

Um cluster de tecido de serviço Azure para serviços de contentores utiliza o modo de rede **nat** por predefinição. Quando mais de um serviço de contentores estiver a ouvir na mesma porta e o modo nat estiver a ser utilizado, podem ocorrer erros de implantação. Para suportar vários serviços de contentores que ouvem na mesma porta, o Service Fabric oferece o modo de rede **Aberto** (versões 5.7 e posteriores). No modo Aberto, cada serviço de contentores tem um endereço IP interno e dinamicamente atribuído que suporta vários serviços de escuta na mesma porta.  

Se tiver um serviço de contentores com um ponto final estático no seu manifesto de serviço, pode criar e eliminar novos serviços utilizando o modo Open sem erros de implantação. O mesmo ficheiro docker-compose.yml também pode ser usado com mapeamentos estáticos de porta para criar vários serviços.

Quando um serviço de contentores reinicia ou se desloca para outro nó no cluster, o endereço IP muda. Por esta razão, não recomendamos a utilização do endereço IP atribuído dinamicamente para descobrir os serviços de contentores. Apenas o Serviço de Nomeação de Tecido de Serviço ou o Serviço DNS devem ser utilizados para a descoberta do serviço. 

>[!WARNING]
>O Azure permite um total de 65.356 IPs por rede virtual. A soma do número de nós e do número de casos de serviço de contentores (que utilizam o modo Aberto) não pode exceder 65.356 IPs dentro de uma rede virtual. Para cenários de alta densidade, recomendamos o modo de networking nat. Além disso, outras dependências, como o equilibrador de carga, terão [outras limitações](../azure-resource-manager/management/azure-subscription-service-limits.md) a considerar. Atualmente, até 50 IPs por nó foram testados e comprovados estáveis. 
>

## <a name="set-up-open-networking-mode"></a>Configurar o modo de rede aberto

1. Configurar o modelo do Gestor de Recursos Azure. Na secção **de tecidosSettings** do recurso Cluster, ative o Serviço DNS e o Provedor de IP: 

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
    
2. Configurar a secção de perfil de rede do recurso Conjunto de Escala de Máquina Virtual. Isto permite configurar vários endereços IP em cada nó do cluster. O exemplo a seguir configura cinco endereços IP por nó para um cluster de tecido de serviço Windows/Linux. Pode ter cinco casos de serviço a ouvir na porta em cada nó. Para que os cinco IPs estejam acessíveis a partir do Balançador de Carga Azure, inscreva os cinco IPs no Pool de Endereços de Backend Balancer de Carga Azure, conforme mostrado abaixo.  Também terá de adicionar as variáveis ao topo do seu modelo na secção de variáveis.

    Adicione esta secção a Variáveis:

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
    
    Adicione esta secção ao recurso Conjunto de Escala de Máquina Virtual:

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
 
3. Apenas para clusters Windows, crie uma regra do Azure Network Security Group (NSG) que abre a porta UDP/53 para a rede virtual com os seguintes valores:

   |Definição |Valor |
   | --- | --- |
   |Prioridade |2000 |
   |Name |Custom_Dns  |
   |Origem |VirtualNetwork |
   |Destino | VirtualNetwork |
   |Serviço | DNS (UDP/53) |
   |Ação | Permitir  |

4. Especificar o modo de ligação em rede no manifesto de aplicação para cada serviço: `<NetworkConfig NetworkType="Open">` . **O** modo de rede aberto resulta na obtenção de um endereço IP dedicado. Se um modo não for especificado, o serviço está em incumprimento do modo **nat.** No exemplo manifesto que se segue, os `NodeContainerServicePackage1` serviços podem `NodeContainerServicePackage2` ouvir cada um na mesma porta (ambos os serviços estão a `Endpoint1` ouvir). Quando o modo de rede aberto é especificado, `PortBinding` as configurações não podem ser especificadas.

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

    Pode misturar e combinar diferentes modos de rede através de serviços dentro de uma aplicação para um cluster Windows. Alguns serviços podem utilizar o modo Open enquanto outros usam o modo nat. Quando um serviço é configurado para utilizar o modo nat, a porta onde o serviço está a ouvir deve ser única.

    >[!NOTE]
    >Nos clusters Linux, não é suportado o modo de rede de mistura para diferentes serviços. 
    >

5. Quando o modo **Open** é selecionado, a definição **endpoint** no manifesto de serviço deve indicar explicitamente o pacote de código correspondente ao ponto final, mesmo que o pacote de serviço tenha apenas um pacote de código nele. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Para o Windows, um reboot VM fará com que a rede aberta seja recriada. Isto é para atenuar um problema subjacente na pilha de rede. O comportamento padrão é recriar a rede. Se este comportamento tiver de ser desligado, a seguinte configuração pode ser utilizada seguida de uma atualização config.

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
 
## <a name="next-steps"></a>Passos seguintes
* [Understand the Service Fabric application model (Compreender o modelo de aplicações do Service Fabric)](service-fabric-application-model.md)
* [Saiba mais sobre o serviço Service Fabric manifesto recursos](./service-fabric-service-manifest-resources.md)
* [Implementar um contentor Windows para o tecido de serviço no Windows Server 2016](service-fabric-get-started-containers.md)
* [Colocar um recipiente Docker para o tecido de serviço em Linux](service-fabric-get-started-containers-linux.md)
