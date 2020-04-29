---
title: Tecido de serviço Azure configuração de procuração inversa
description: Compreenda como configurar e configurar o serviço de procuração inversa para uma aplicação Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: 6e3edb0fe238dcaddb7d99cc68660591f081581c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476682"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Configurar e configurar procuração inversa no Tecido de Serviço Azure
O proxy inverso é um serviço opcional azure service fabric que ajuda os microserviços a funcionar num cluster de Tecido de Serviço a descobrir e a comunicar com outros serviços que têm pontos finais em http. Para saber mais, consulte [O Proxy Inverso no Tecido de Serviço Azure](service-fabric-reverseproxy.md). Este artigo mostra-lhe como configurar e configurar procuração inversa no seu cluster. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Ativar proxy inverso usando o portal Azure

O portal Azure oferece uma opção para ativar o proxy inverso quando criar um novo cluster de Tecido de Serviço. Não é possível atualizar um cluster existente para usar procuração inversa através do portal. 

Para configurar o proxy inverso quando [criar um cluster utilizando o portal Azure,](./service-fabric-cluster-creation-via-portal.md)certifique-se de que faz o seguinte:

1. No **passo 2: Configuração**do cluster, sob a configuração do **tipo nó,** selecione **Ativar proxy invertido**.

   ![Ativar procuração inversa no portal](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Opcional) Para configurar o proxy inverso seguro, é necessário configurar um certificado TLS/SSL. No **passo 3: Segurança**, em **configurar definições**de segurança do cluster , sob o tipo de **configuração,** selecione **Custom**. Em seguida, sob **o certificado SSL de Procuração Inversa,** selecione **Incluir um certificado SSL para procuração inversa** e insira os detalhes do certificado.

   ![Configure proxy inverso seguro no portal](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Se optar por não configurar o proxy inverso com um certificado quando criar o cluster, pode fazê-lo mais tarde através do modelo de Gestor de Recursos para o grupo de recursos do cluster. Para saber mais, consulte Ativar o proxy inverso através dos [modelos do Gestor](#enable-reverse-proxy-via-azure-resource-manager-templates)de Recursos Azure .

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Ativar proxy invertido através de modelos de Gestor de Recursos Azure

Para clusters no Azure, pode utilizar o modelo do Gestor de Recursos Azure para ativar o proxy inverso no Tecido de Serviço. Pode ativar o proxy inverso quando criar o cluster ou capacitá-lo atualizando o cluster mais tarde. 

Para um novo cluster, pode [criar um modelo personalizado de Gestor](service-fabric-cluster-creation-via-arm.md) de Recursos ou pode usar um modelo de amostra. 

Pode encontrar modelos do Gestor de Recursos da amostra que podem ajudá-lo a configurar proxy inverso seguro para um cluster Azure nos modelos de amostra de [procuração inversa seguro](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) no GitHub. Consulte o [Configure HTTPS Reverse Proxy num cluster seguro](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) no ficheiro README para obter instruções e os modelos a utilizar para configurar o proxy inverso seguro com um certificado e para lidar com o capotamento do certificado.

Para um cluster existente, pode exportar o modelo de Gestor de Recursos para o grupo de recursos do cluster utilizando o [portal Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) [PowerShell,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)ou o [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

Depois de ter um modelo de Gestor de Recursos, pode ativar o proxy inverso com os seguintes passos:

1. Defina uma porta para o proxy inverso na [secção Parâmetros](../azure-resource-manager/templates/template-syntax.md) do modelo.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especifique a porta para cada um dos objetos do nótipo na [secção](../azure-resource-manager/templates/template-syntax.md)de recursos [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) .

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
3. Para configurar os certificados TLS/SSL na porta para o proxy invertido, adicione o certificado à propriedade ***reverseProxyCertificate*** na secção de [recursos](../resource-group-authoring-templates.md) **Microsoft.ServiceFabric/clusters** .

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Suportando um certificado de procuração inversa que é diferente do certificado de cluster
 Se o certificado de procuração inversa for diferente do certificado que protege o cluster, o certificado previamente especificado deve ser instalado na máquina virtual e adicionado à lista de controlo de acesso (ACL) para que o Tecido de Serviço possa aceder-lhe. Isto pode ser feito na secção de recursos do [tipo](../resource-group-authoring-templates.md) [**Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) . Para a instalação, adicione o certificado ao osPerfil. A secção de extensão do modelo pode atualizar o certificado no ACL.

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
> Quando utilizar certificados diferentes do certificado de cluster para ativar o proxy inverso num cluster existente, instale o certificado de procuração inversa e atualize o ACL no cluster antes de ativar o proxy inverso. Complete a implementação do modelo do Gestor de [Recursos Azure](service-fabric-cluster-creation-via-arm.md) utilizando as definições mencionadas anteriormente antes de iniciar uma implementação para ativar o proxy inverso nos passos 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Ativar procuração inversa em clusters autónomos

Para clusters autónomos, ativa o proxy inverso no ficheiro ClusterConfig.json. Pode ativar o proxy inverso na criação de clusterou através da atualização da configuração para um cluster existente. Para saber mais sobre as definições disponíveis nos ficheiros ClusterConfig.json, consulte [as definições](./service-fabric-cluster-manifest.md)de cluster autónoma .

Os seguintes passos mostram-lhe as definições a utilizar para ativar o proxy invertido e, opcionalmente, para fixar o proxy inverso com um certificado X.509. 

1. Para ativar o proxy invertido, detete o valor **inverso proxyEndpointPort** para o tipo de nó sob **propriedades** no config cluster. O Seguinte JSON mostra a definição da porta final de procuração inversa para 19081 para os nódosos com um tipo de "NodeType0":

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
2. (Opcional) Para um representante inverso seguro, configure um certificado na secção de **segurança** em **propriedades**. 
   - Para um ambiente de desenvolvimento ou teste, pode utilizar a definição **ReverseProxyCertificate:**

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
   - Para um ambiente de produção, recomenda-se a definição **ReverseProxyCertificateCommonNames:**

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

   Para saber mais sobre configurar e gerir certificados para um cluster autónomo, bem como mais detalhes sobre a configuração de certificados usados para garantir procuração inversa, consulte a [segurança baseada em certificados X509](./service-fabric-windows-cluster-x509-security.md).

Depois de ter modificado o ficheiro ClusterConfig.json para ativar o proxy invertido, siga as instruções em [Atualizar a configuração do cluster](service-fabric-cluster-config-upgrade-windows-server.md) para empurrar as alterações para o seu cluster.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Expor procuração inversa num porto público através do Azure Load Balancer

Para abordar o proxy inverso de fora de um cluster Azure, criar regras azure Load Balancer e uma Sonda de Saúde Azure para a porta de procuração inversa. Estes passos podem ser realizados utilizando o portal Azure ou o modelo de Gestor de Recursos a qualquer momento depois de ter criado o cluster. 

> [!WARNING]
> Quando configurar a porta do proxy inverso no Balancer de Carga, todos os microserviços do cluster que expõem um ponto final HTTP são endereçados de fora do cluster. Isto significa que os microserviços destinados a serem internos podem ser detetáveis por um determinado utilizador malicioso. Isto potencialmente apresenta vulnerabilidades graves que podem ser exploradas; Por exemplo:
>
> * Um utilizador malicioso pode lançar um ataque de negação de serviço, chamando repetidamente um serviço interno que não tenha uma superfície de ataque suficientemente endurecida.
> * Um utilizador malicioso pode entregar pacotes mal formados a um serviço interno, resultando em comportamentos não intencionais.
> * Um serviço destinado a ser interno pode devolver informações privadas ou sensíveis não destinadas a serem expostas a serviços fora do cluster, expondo assim esta informação sensível a um utilizador mal-intencionado. 
>
> Certifique-se de que compreende e atenua as potenciais ramificações de segurança para o seu cluster e as aplicações que estão a decorrer nele, antes de tornar público o porto de procuração inversa. 
>

Se quiser expor publicamente o proxy inverso para um cluster autónomo, a forma como o faz dependerá do sistema que acolhe o cluster e está fora do âmbito deste artigo. No entanto, o aviso anterior sobre a exposição de procuração inversa publicamente, no entanto, ainda se aplica.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Expor o proxy inverso usando o portal Azure 

1. No portal Azure, clique no grupo de recursos para o seu cluster e, em seguida, clique no equilibrador de carga para o seu cluster.
2. Para adicionar uma sonda de saúde para a porta de procuração inversa, no painel esquerdo da janela do equilíbrio de carga, em **DEFINIÇÕES,** clique em sondas de **saúde**. Em seguida, clique em **Adicionar** na parte superior da janela de sondas Health e introduzir detalhes para a porta de procuração inversa e, em seguida, **clique**OK . Por padrão, a porta de procuração inversa é 19081, a menos que a tenha mudado quando criou o cluster.

   ![Configure sonda de saúde proxy reversa](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Para adicionar uma regra do Balancer de carga para expor a porta de procuração inversa, no painel esquerdo da janela do equilíbrio de carga, em **DEFINIÇÕES,** clique em regras de **equilíbrio de carga**. Em seguida, clique em **Adicionar** na parte superior da janela de regras de equilíbrio de carga e introduzir detalhes para a porta de procuração inversa. Certifique-se de que define o valor **do Porto** para a porta em que deseja que o proxy inverso seja exposto, o valor da **porta Backend** para a porta que definiu quando ativou o proxy inverso, e o valor da **sonda Health** para a sonda de saúde que configurado no passo anterior. Desloque outros campos conforme apropriado e clique em **OK**.

   ![Configure regra do equilíbrior de carga para procuração inversa](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Expor o proxy inverso através de modelos de Gestor de Recursos

O JSON seguinte refere o mesmo modelo que é usado em Ativar proxy invertido através de [modelos do Gestor de Recursos Azure](#enable-reverse-proxy-via-azure-resource-manager-templates). Consulte a secção do documento para obter informações sobre como criar um modelo de Gestor de Recursos ou exportar um modelo para um cluster existente.  As alterações são feitas na secção de [recursos](../resource-group-authoring-templates.md) [**microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) .

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


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Personalize o comportamento de procuração inversa usando definições de tecido

Pode personalizar o comportamento de procuração inversa através de configurações de tecido no modelo de Gestor de Recursos para clusters hospedados em Azure ou no ficheiro ClusterConfig.json para clusters autónomos. As definições que controlam o comportamento de procuração inversa estão localizadas na secção [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) na secção **Definições** de tecido sem secção de **propriedades** do cluster. 

Por exemplo, pode definir o valor do **Predefinido HttpRequestTimeout** para definir o prazo de pedidos para o proxy invertido para 180 segundos como no seguinte JSON:

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

Para obter mais informações sobre a atualização das definições de tecido para clusters Azure, consulte personalizar as definições de [cluster utilizando modelos de Gestor de Recursos](service-fabric-cluster-config-upgrade-azure.md). Para clusters autónomos, consulte Personalizar as definições de [cluster para clusters autónomos](service-fabric-cluster-config-upgrade-windows-server.md). 

Várias configurações de tecido são usadas para ajudar a estabelecer uma comunicação segura entre procuração inversa e serviços. Para obter informações detalhadas sobre estas definições, consulte [O Connect para um serviço seguro com o proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Passos seguintes
* [Configurar o encaminhamento para assegurar o serviço HTTP com o proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md)
* Para opções de configuração de proxy invertidos, consulte a [secção ApplicationGateway/Http nas definições](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)de cluster de personalizar tecido de serviço .
