---
title: O Azure Service Fabric configurar o proxy inverso | Documentos da Microsoft
description: Aprenda a definir e configurar o proxy inverso do Service Fabric.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/13/2018
ms.author: v-jamebr
ms.openlocfilehash: 8c405941417ad1a4e877cfd8fd0bdb53e186f6d0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452305"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Definir e configurar o proxy inverso no Azure Service Fabric
Proxy inverso é um serviço de Azure Service Fabric opcional que o ajuda a microsserviços em execução no cluster do Service Fabric detetar e comunicar com outros serviços que têm pontos finais de http. Para obter mais informações, consulte [proxy no Azure Service Fabric inverso](service-fabric-reverseproxy.md). Este artigo mostra-lhe como configurar e configurar o proxy inverso no cluster. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Ativar proxy inverso, com o portal do Azure

Portal do Azure fornece uma opção para ativar o proxy inverso quando cria um novo cluster do Service Fabric. Não é possível atualizar um cluster existente para utilizar o proxy reverso através do portal. 

Para configurar o proxy inverso quando [criar um cluster com o portal do Azure](./service-fabric-cluster-creation-via-portal.md), certifique-se de que faça o seguinte:

1. No **passo 2: Configuração de cluster**, sob **configuração do tipo de nó**, selecione **ativação proxy inverso**.

   ![Ativar proxy inverso no portal](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Opcional) Para configurar o proxy inverso seguro, terá de configurar um certificado SSL. No **passo 3: Segurança**, no **configurar as definições de segurança do cluster**, sob **tipo de configuração**, selecione **personalizado**. Em seguida, em **certificado SSL de Proxy inverso**, selecione **incluem um certificado SSL para proxy inverso** e introduza os detalhes do seu certificado.

   ![Configurar o proxy inverso seguro no portal](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Se optar por não configurar o proxy inverso com um certificado ao criar o cluster, pode fazer por isso, mais tarde através do modelo do Resource Manager para o grupo de recursos do cluster. Para obter mais informações, consulte [ativar o proxy reverso através de modelos Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Ativar proxy inverso através de modelos Azure Resource Manager

Para os clusters no Azure, pode utilizar o modelo Azure Resource Manager para ativar o proxy inverso no Service Fabric. Pode ativar o proxy inverso ao criar o cluster ou ativá-la ao atualizar o cluster mais tarde. 

Para obter um novo cluster, pode [criar um modelo de Gestor de recursos personalizado](service-fabric-cluster-creation-via-arm.md) ou pode utilizar um modelo de exemplo. 

Pode encontrar modelos do Resource Manager de exemplo que podem ajudá-lo a configurar o proxy inverso seguro para um cluster do Azure no [seguro modelos de exemplo de Proxy inverso](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample) no GitHub. Consulte a [configurar HTTPS Proxy inverso num cluster seguro](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) no ficheiro Leia-me para obter instruções e os modelos a utilizar para configurar o proxy inverso seguro com um certificado e lidar com o certificado de rollover.

Para um cluster existente, pode exportar o modelo do Resource Manager para o recurso do cluster de grupo utilizando o [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell), ou o [da CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

Depois de ter um modelo do Resource Manager, pode ativar o proxy inverso, com os seguintes passos:

1. Definir uma porta para o proxy inverso no [secção de parâmetros](../azure-resource-manager/resource-group-authoring-templates.md) do modelo.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especifique a porta para cada um dos objetos no nodetype a [ **Microsoft.ServiceFabric/clusters** ](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [secção de tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md).

    A porta é identificada pelo nome do parâmetro, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Para configurar certificados SSL na porta para o proxy inverso, adicionar o certificado para o ***reverseProxyCertificate*** propriedade na **Microsoft.ServiceFabric/clusters** [recursos Escreva a seção](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Suporte a um certificado de proxy inverso que é diferente do certificado de cluster
 Se o certificado de proxy inverso é diferente do certificado que protege o cluster, em seguida, o certificado especificado anteriormente deve ser instalado na máquina virtual e adicionado à lista de controlo de acesso (ACL) para que possa aceder a recursos de infraestrutura do serviço. Isso pode ser feito no [ **Compute/virtualmachinescalesets** ](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [secção de tipo de recurso](../resource-group-authoring-templates.md). Para a instalação, adicione o certificado para o osProfile. A secção de extensão do modelo pode atualizar o certificado na ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Quando utilizar certificados diferentes do certificado de cluster para ativar o proxy inverso num cluster existente, instalar o certificado de proxy inverso e atualizar a ACL no cluster antes de ativar o proxy inverso. Concluir o [modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) implantação usando as configurações mencionadas anteriormente antes de iniciar uma implementação para ativar o proxy inverso nos passos 1 a 3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Ativar proxy inverso no clusters autónomos

Para clusters autónomos, ativar o proxy inverso no ficheiro ClusterConfig.json. Pode ativar o proxy inverso durante a criação de cluster ou Atualize a configuração para um cluster existente. Para saber mais sobre as definições disponíveis nos ficheiros de ClusterConfig.json, veja [definições do cluster autónomo](./service-fabric-cluster-manifest.md).

Os passos seguintes mostram-lhe as definições a utilizar para ativar o proxy inverso e, opcionalmente, para proteger o proxy inverso, com um certificado X.509. 

1. Para ativar o proxy inverso, defina o **reverseProxyEndpointPort** valor para o tipo de nó sob **propriedades** na configuração do cluster. O JSON seguinte mostra a definição do proxy inverso porta de ponto final para 19081 para nós com um tipo de "NodeType0":

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (Opcional) Para um proxy inverso seguro, configurar um certificado no **security** secção sob **propriedades**. 
   - Para um ambiente de desenvolvimento ou teste, pode utilizar o **ReverseProxyCertificate** definição:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Para um ambiente de produção, o **ReverseProxyCertificateCommonNames** definição é recomendada:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Para saber mais sobre configurar e gerir certificados para um cluster autónomo, bem como mais detalhes sobre como configurar certificados usados para proteger o proxy inverso, veja [X509 segurança baseada em certificado](./service-fabric-windows-cluster-x509-security.md).

Depois de modificar o ficheiro de ClusterConfig.json para ativar o proxy inverso, siga as instruções em [atualizar a configuração de cluster](service-fabric-cluster-config-upgrade-windows-server.md) para enviar por push as alterações ao seu cluster.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Expor o proxy inverso numa porta pública através do Balanceador de carga do Azure

Para resolver o proxy inverso de fora de um cluster do Azure, configure regras de Balanceador de carga do Azure e uma sonda de estado de funcionamento do Azure para a porta de proxy inverso. Estes passos podem ser efetuados utilizando o portal do Azure ou o modelo do Resource Manager em qualquer altura depois de criar o cluster. 

> [!WARNING]
> Quando configura a porta do proxy inverso no balanceador de carga, todos os microsserviços no cluster que expõem um ponto de extremidade HTTP são endereçáveis a partir de fora do cluster. Isso significa que os microsserviços devem ser interno podem ser Detetáveis por um determinado utilizador mal intencionado. Isso apresenta potencialmente sérias vulnerabilidades que podem ser exploradas; Por exemplo:
>
> * Um utilizador mal intencionado pode iniciar um ataque de negação de serviço chamando um serviço interno que não tenha uma superfície de ataque suficientemente fortalecidos repetidamente.
> * Um utilizador mal intencionado pode enviar pacotes com formato incorreto para um serviço interno, resultando em comportamentos indesejados.
> * Um serviço devem ser interno pode retornar informações confidenciais ou privadas, que não se destina a ser exposto aos serviços fora do cluster, expondo, portanto, essas informações confidenciais para um utilizador mal intencionado. 
>
> Certifique-se totalmente entender e atenuar as possíveis ramificações de segurança do cluster e as aplicações em execução, antes de fazer a porta de proxy inverso pública. 
>

Se quiser expor o proxy inverso para um cluster autónomo, a maneira na qual fazer isso dependerá do sistema que aloja o cluster e está além do escopo deste artigo. O aviso anterior sobre a exposição de proxy inverso publicamente, no entanto, ainda se aplica.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Expor o proxy inverso, com o portal do Azure 

1. No portal do Azure, clique no grupo de recursos do seu cluster, em seguida, clique no balanceador de carga para o seu cluster.
2. Para adicionar um sonda para a porta de proxy inverso, do Estado de funcionamento no painel esquerdo da janela de Balanceador de carga, na **configurações**, clique em **sondas de estado de funcionamento**. Em seguida, clique em **Add** na parte superior do Estado de funcionamento sondas de janela e introduza os detalhes para a porta de proxy inverso, em seguida, clique em **OK**. Por predefinição, a porta de proxy inverso é 19081, a menos que alterado quando criou o cluster.

   ![Configurar a sonda de estado de funcionamento do proxy inverso](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Para adicionar uma regra de Balanceador de carga para expor a porta de proxy inverso, no painel esquerdo da janela de Balanceador de carga, em **configurações**, clique em **regras de balanceamento de carga**. Em seguida, clique em **adicionar** na parte superior da carga do balanceamento de regras de janela e introduza os detalhes para a porta de proxy inverso. Certifique-se de definir o **porta** valor para a porta que pretende que o proxy inverso exposto, o **porta de back-end** valor para a porta que definiu quando ativou o proxy inverso, e o **desondadeestadodefuncionamento** valor para a sonda de estado de funcionamento que configurou no passo anterior. Definir os outros campos conforme adequado e clique em **OK**.

   ![Configurar a regra de Balanceador de carga para o proxy inverso](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Expor o proxy inverso através de modelos do Resource Manager

O JSON seguinte referencia o mesmo modelo que é utilizado na [ativar o proxy reverso através de modelos Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates). Consulte a secção do documento para obter informações sobre como criar um modelo do Resource Manager ou exportar um modelo para um cluster existente.  As alterações são feitas para o [ **Network/loadbalancers** ](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [secção de tipo de recurso](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Personalizar o comportamento de proxy inverso, com as definições de recursos de infraestrutura

Pode personalizar o comportamento de proxy inverso através das definições de recursos de infraestrutura no modelo do Resource Manager para clusters alojados no Azure ou no ficheiro ClusterConfig.json para clusters autónomos. As definições que controlam o comportamento de proxy inverso estão localizadas no [ **gateway de aplicação/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) secção o **fabricSettings** secção sob o cluster **propriedades** secção. 

Por exemplo, pode definir o valor de **DefaultHttpRequestTimeout** para definir o tempo limite de pedidos para o proxy inverso para 180 segundos, como no seguinte JSON:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Para obter mais informações sobre a atualizar as definições de recursos de infraestrutura para os clusters do Azure, consulte [personalizar definições do cluster através de modelos do Resource Manager](service-fabric-cluster-config-upgrade-azure.md). Para clusters autónomos, consulte [personalizar definições do cluster para clusters autónomos](service-fabric-cluster-config-upgrade-windows-server.md). 

Várias definições de recursos de infraestrutura são utilizadas para ajudar a estabelecer uma comunicação segura entre o proxy inverso e serviços. Para obter informações detalhadas sobre estas definições, consulte [ligar a um serviço seguro com o proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Passos Seguintes
* [Configurar o encaminhamento para o serviço HTTP seguro com o proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md)
* Para opções de configuração de proxy inverso, consulte [secção do gateway de aplicação/Http nas definições de cluster do Service Fabric personalizar](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
