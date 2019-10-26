---
title: Azure Service Fabric configurar proxy reverso | Microsoft Docs
description: Entenda como configurar e configurar o proxy reverso do Service Fabric.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: a80eb1ba341a1b15da7ad59203a1d25284d03fd0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933904"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Configurar e configurar o proxy reverso no Azure Service Fabric
O proxy reverso é um serviço de Service Fabric do Azure opcional que ajuda os microserviços em execução em uma descoberta de Cluster Service Fabric e se comunicam com outros serviços que têm pontos de extremidade http. Para saber mais, confira [proxy reverso no Azure Service Fabric](service-fabric-reverseproxy.md). Este artigo mostra como configurar e configurar o proxy reverso no cluster. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Habilitar proxy reverso usando portal do Azure

Portal do Azure fornece uma opção para habilitar o proxy reverso quando você cria um novo cluster de Service Fabric. Não é possível atualizar um cluster existente para usar o proxy reverso por meio do Portal. 

Para configurar o proxy reverso ao [criar um cluster usando portal do Azure](./service-fabric-cluster-creation-via-portal.md), certifique-se de fazer o seguinte:

1. Na **etapa 2: configuração de cluster**, em **configuração de tipo de nó**, selecione **habilitar proxy reverso**.

   ![Habilitar proxy reverso no portal](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. Adicional Para configurar o proxy reverso seguro, você precisa configurar um certificado SSL. Na **etapa 3: segurança**, em **definir configurações de segurança do cluster**, em **tipo de configuração**, selecione **personalizado**. Em seguida, em **certificado SSL de proxy reverso**, selecione **incluir um certificado SSL para o proxy reverso** e insira os detalhes do certificado.

   ![Configurar o proxy reverso seguro no portal](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Se você optar por não configurar o proxy reverso com um certificado ao criar o cluster, poderá fazer isso mais tarde por meio do modelo do Resource Manager para o grupo de recursos do cluster. Para saber mais, confira [habilitar o proxy reverso por meio de modelos de Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Habilitar proxy reverso por meio de modelos de Azure Resource Manager

Para clusters no Azure, você pode usar o modelo de Azure Resource Manager para habilitar o proxy reverso no Service Fabric. Você pode habilitar o proxy reverso ao criar o cluster ou habilitá-lo atualizando o cluster em um momento posterior. 

Para um novo cluster, você pode [criar um modelo personalizado do Resource Manager](service-fabric-cluster-creation-via-arm.md) ou pode usar um modelo de exemplo. 

Você pode encontrar modelos do Resource Manager de exemplo que podem ajudá-lo a configurar o proxy reverso seguro para um cluster do Azure nos [modelos de exemplo de proxy reverso seguro](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) no github. Consulte [Configurar o proxy reverso HTTPS em um cluster seguro](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) no arquivo Leiame para obter instruções e os modelos a serem usados para configurar o proxy reverso seguro com um certificado e para lidar com a substituição do certificado.

Para um cluster existente, você pode exportar o modelo do Resource Manager para o grupo de recursos do cluster usando o [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), o [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)ou o [CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

Depois de ter um modelo do Resource Manager, você pode habilitar o proxy reverso com as seguintes etapas:

1. Defina uma porta para o proxy reverso na [seção de parâmetros](../azure-resource-manager/resource-group-authoring-templates.md) do modelo.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especifique a porta para cada um dos objetos NodeType na [seção tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md) [**Microsoft. ServicePortal/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) .

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
3. Para configurar certificados SSL na porta para o proxy reverso, adicione o certificado à propriedade ***reverseProxyCertificate*** na [seção tipo de recurso](../resource-group-authoring-templates.md) **Microsoft. ServicePortal/clusters** .

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Suporte a um certificado de proxy reverso diferente do certificado de cluster
 Se o certificado de proxy reverso for diferente do certificado que protege o cluster, o certificado especificado anteriormente deverá ser instalado na máquina virtual e adicionado à ACL (lista de controle de acesso) para que Service Fabric possa acessá-lo. Isso pode ser feito na [seção tipo de recurso](../resource-group-authoring-templates.md) [**Microsoft. Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) . Para a instalação, adicione o certificado ao osProfile. A seção extensão do modelo pode atualizar o certificado na ACL.

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
> Quando você usa certificados que são diferentes do certificado de cluster para habilitar o proxy reverso em um cluster existente, instale o certificado de proxy reverso e atualize a ACL no cluster antes de habilitar o proxy reverso. Conclua a implantação do [modelo de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) usando as configurações mencionadas anteriormente antes de iniciar uma implantação para habilitar o proxy reverso nas etapas 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Habilitar proxy reverso em clusters autônomos

Para clusters autônomos, você habilita o proxy reverso no arquivo ClusterConfig. JSON. Você pode habilitar o proxy reverso na criação do cluster ou atualizando a configuração de um cluster existente. Para saber mais sobre as configurações disponíveis em arquivos ClusterConfig. JSON, consulte [configurações de cluster autônomos](./service-fabric-cluster-manifest.md).

As etapas a seguir mostram as configurações a serem usadas para habilitar o proxy reverso e, opcionalmente, para proteger o proxy reverso com um certificado X. 509. 

1. Para habilitar o proxy reverso, defina o valor **reverseProxyEndpointPort** para o tipo de nó em **Propriedades** na configuração do cluster. O JSON a seguir mostra a configuração da porta do ponto de extremidade do proxy reverso para 19081 para nós com um tipo de "NodeType0":

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
2. Adicional Para um proxy reverso seguro, configure um certificado na seção **segurança** em **Propriedades**. 
   - Para um ambiente de desenvolvimento ou teste, você pode usar a configuração **ReverseProxyCertificate** :

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
   - Para um ambiente de produção, a configuração **ReverseProxyCertificateCommonNames** é recomendada:

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

   Para saber mais sobre como configurar e gerenciar certificados para um cluster autônomo, bem como mais detalhes sobre a configuração de certificados usados para proteger o proxy reverso, consulte [segurança baseada em certificado X509](./service-fabric-windows-cluster-x509-security.md).

Depois de modificar o arquivo ClusterConfig. JSON para habilitar o proxy reverso, siga as instruções em [atualizar a configuração do cluster](service-fabric-cluster-config-upgrade-windows-server.md) para enviar por push as alterações para o cluster.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Expor proxy reverso em uma porta pública por meio de Azure Load Balancer

Para resolver o proxy reverso de fora de um cluster do Azure, configure Azure Load Balancer regras e uma investigação de integridade do Azure para a porta de proxy reverso. Essas etapas podem ser executadas usando o portal do Azure ou o modelo do Resource Manager a qualquer momento após a criação do cluster. 

> [!WARNING]
> Quando você configura a porta do proxy reverso no Load Balancer, todos os microserviços no cluster que expõem um ponto de extremidade HTTP são endereçáveis de fora do cluster. Isso significa que os microserviços destinados a serem internos podem ser detectáveis por um determinado usuário mal-intencionado. Isso potencialmente apresenta vulnerabilidades sérias que podem ser exploradas; por exemplo:
>
> * Um usuário mal-intencionado pode iniciar um ataque de negação de serviço chamando repetidamente um serviço interno que não tem uma superfície de ataque suficientemente protegida.
> * Um usuário mal-intencionado pode entregar pacotes malformados a um serviço interno, resultando em um comportamento indesejado.
> * Um serviço destinado a ser interno pode retornar informações privadas ou confidenciais que não devem ser expostas a serviços fora do cluster, expondo assim essas informações confidenciais a um usuário mal-intencionado. 
>
> Certifique-se de compreender e atenuar totalmente as possíveis ramificações de segurança para o cluster e os aplicativos em execução, antes de tornar a porta de proxy inversa pública. 
>

Se você quiser expor o proxy reverso publicamente para um cluster autônomo, a maneira como você faz isso dependerá do sistema que hospeda o cluster e está além do escopo deste artigo. O aviso anterior sobre a exposição do proxy reverso publicamente, no entanto, ainda se aplica.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Expor o proxy reverso usando portal do Azure 

1. Na portal do Azure, clique no grupo de recursos para o cluster e, em seguida, clique no balanceador de carga do cluster.
2. Para adicionar uma investigação de integridade para a porta de proxy reverso, no painel esquerdo da janela do balanceador de carga, em **configurações**, clique em **investigações de integridade**. Em seguida, clique em **Adicionar** na parte superior da janela investigações de integridade e insira os detalhes da porta do proxy reverso e clique em **OK**. Por padrão, a porta de proxy reverso é 19081, a menos que você a tenha alterado quando criou o cluster.

   ![Configurar investigação de integridade de proxy reverso](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Para adicionar uma regra de Load Balancer para expor a porta de proxy reverso, no painel esquerdo da janela do balanceador de carga, em **configurações**, clique em **regras de balanceamento de carga**. Em seguida, clique em **Adicionar** na parte superior da janela regras de balanceamento de carga e insira os detalhes da porta de proxy reverso. Certifique-se de definir o valor da **porta** para a porta na qual você deseja que o proxy reverso seja exposto, o valor da **porta de back-end** para a porta que você definiu quando habilitou o proxy reverso e o valor de **investigação de integridade** para a investigação de integridade que você configurou na etapa anterior. Defina outros campos conforme apropriado e clique em **OK**.

   ![Configurar a regra do balanceador de carga para o proxy reverso](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Expor o proxy reverso por meio de modelos do Resource Manager

O JSON a seguir faz referência ao mesmo modelo usado em [habilitar proxy reverso por meio de modelos de Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates). Consulte essa seção do documento para obter informações sobre como criar um modelo do Resource Manager ou exportar um modelo para um cluster existente.  As alterações são feitas na [seção tipo de recurso](../resource-group-authoring-templates.md) [**Microsoft. Network/balancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) .

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


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Personalizar o comportamento do proxy reverso usando as configurações de malha

Você pode personalizar o comportamento do proxy reverso por meio de configurações de malha no modelo do Resource Manager para clusters hospedados no Azure ou no arquivo ClusterConfig. JSON para clusters autônomos. As configurações que controlam o comportamento do proxy reverso estão localizadas na seção [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) na seção **fabricSettings** na seção **Propriedades** do cluster. 

Por exemplo, você pode definir o valor de **DefaultHttpRequestTimeout** para definir o tempo limite para solicitações para o proxy reverso a 180 segundos como no JSON a seguir:

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

Para obter mais informações sobre como atualizar as configurações de malha para clusters do Azure, consulte [Personalizar configurações de cluster usando modelos do Resource Manager](service-fabric-cluster-config-upgrade-azure.md). Para clusters autônomos, consulte [Personalizar configurações de cluster para clusters autônomos](service-fabric-cluster-config-upgrade-windows-server.md). 

Várias configurações de malha são usadas para ajudar a estabelecer uma comunicação segura entre o proxy reverso e os serviços. Para obter informações detalhadas sobre essas configurações, consulte [conectar-se a um serviço seguro com o proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Passos seguintes
* [Configurar o encaminhamento para o serviço HTTP seguro com o proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md)
* Para obter opções de configuração de proxy reverso, consulte a [seção ApplicationGateway/http em personalizar Service Fabric configurações de cluster](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
