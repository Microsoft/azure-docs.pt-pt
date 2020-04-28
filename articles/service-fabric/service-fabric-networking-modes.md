---
title: Configure modos de rede para serviços de contentores
description: Saiba como configurar os diferentes modos de networking que são suportados pelo Azure Service Fabric.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: ba1fa92559d39a481008d1dd18036e4232be1bfa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75639807"
---
# <a name="service-fabric-container-networking-modes"></a>Modos de rede de contentores de tecido de serviço

Um cluster Azure Service Fabric para serviços de contentores utiliza o modo de rede **nat** por padrão. Quando mais de um serviço de contentores está a ouvir no mesmo modo de porta e nat, podem ocorrer erros de implantação. Para suportar vários serviços de contentores a ouvir na mesma porta, o Service Fabric oferece modo de rede **aberto** (versões 5.7 e posterior). No modo Open, cada serviço de contentores tem um endereço IP interno e dinamicamente atribuído que suporta vários serviços de escuta na mesma porta.  

Se tiver um serviço de contentores com um ponto final estático no seu manifesto de serviço, pode criar e eliminar novos serviços utilizando o modo Open sem erros de implementação. O mesmo ficheiro docker-compose.yml também pode ser usado com mapeamentos estáticos da porta para criar vários serviços.

Quando um serviço de contentores reinicia ou se desloca para outro nó no cluster, o endereço IP muda. Por esta razão, não recomendamos a utilização do endereço IP dinamicamente atribuído para descobrir serviços de contentores. Apenas o Serviço de Nomeação de Tecidos de Serviço ou o Serviço DNS devem ser utilizados para a descoberta do serviço. 

>[!WARNING]
>O Azure permite um total de 65.356 IPs por rede virtual. A soma do número de nós e o número de instâncias de serviço de contentores (que estão a utilizar o modo Open) não podem exceder 65.356 IPs dentro de uma rede virtual. Para cenários de alta densidade, recomendamos o modo de networking nat. Além disso, outras dependências, como o equilibrador de carga, terão [outras limitações](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) a considerar. Atualmente, até 50 IPs por nó foram testados e comprovados estáveis. 
>

## <a name="set-up-open-networking-mode"></a>Configurar o modo de rede aberto

1. Configurar o modelo de Gestor de Recursos Azure. Na secção **de definições** de tecido do recurso Cluster, ative o Serviço DNS e o Fornecedor IP: 

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
    
2. Configurar a secção de perfil de rede do recurso Conjunto de Escala de Máquina Virtual. Isto permite configurar vários endereços IP em cada nó do cluster. O exemplo seguinte configura cinco endereços IP por nó para um cluster de tecido de serviço Windows/Linux. Pode ter cinco instâncias de serviço ouvindo na porta em cada nó. Para que os cinco IPs estejam acessíveis a partir do Equilíbrio de Carga Azure, inscreva os cinco IPs no Pool de Endereços backend do equilíbrio de carga Azure, como mostrado abaixo.  Também terá de adicionar as variáveis ao topo do seu modelo na secção de variáveis.

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
    
    Adicione esta secção ao recurso conjunto de escala de máquina virtual:

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
 
3. Apenas para clusters Windows, criar uma regra do Grupo de Segurança da Rede Azure (NSG) que abre a porta UDP/53 para a rede virtual com os seguintes valores:

   |Definição |Valor | |
   | --- | --- | --- |
   |Prioridade |2000 | |
   |Nome |Custom_Dns  | |
   |Origem |VirtualNetwork | |
   |Destino | VirtualNetwork | |
   |Serviço | DNS (UDP/53) | |
   |Ação | Permitir  | |
   | | |

4. Especifique o modo de rede `<NetworkConfig NetworkType="Open">`no manifesto de aplicação para cada serviço: . **O** modo de funcionamento aberto resulta no serviço obter um endereço IP dedicado. Se um modo não for especificado, o serviço não se aplica ao modo **nat.** No exemplo manifesto seguinte, cada um pode `NodeContainerServicePackage1` `NodeContainerServicePackage2` ouvir no mesmo porto `Endpoint1`(ambos os serviços estão a ouvir). Quando o modo de ligação de rede aberto é especificado, `PortBinding` as configurações não podem ser especificadas.

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

    Pode misturar e combinar diferentes modos de networking através de serviços dentro de uma aplicação para um cluster Windows. Alguns serviços podem usar o modo Open enquanto outros usam o modo nat. Quando um serviço está configurado para utilizar o modo nat, a porta que o serviço está a ouvir deve ser única.

    >[!NOTE]
    >Nos clusters Linux, a mistura de modos de rede para diferentes serviços não é suportada. 
    >

5. Quando o modo **Open** é selecionado, a definição **de Ponto Final** no manifesto de serviço deve apontar explicitamente para o pacote de código correspondente ao ponto final, mesmo que o pacote de serviço tenha apenas um pacote de código no mesmo. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Para o Windows, um reboot vm fará com que a rede aberta seja recriada. Isto é para mitigar uma questão subjacente na pilha de rede. O comportamento padrão é recriar a rede. Se este comportamento tiver de ser desligado, a seguinte configuração pode ser utilizada seguida de uma atualização de config.

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
* [Saiba mais sobre os recursos manifestos do serviço Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Implemente um recipiente Windows para o Serviço de Tecido no Windows Server 2016](service-fabric-get-started-containers.md)
* [Desloque um recipiente Docker para o tecido de serviço em Linux](service-fabric-get-started-containers-linux.md)
