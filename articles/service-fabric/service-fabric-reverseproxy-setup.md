---
title: Azure Service Fabric configurar proxy reverso
description: Compreenda como configurar e configurar o serviço de procuração inversa para uma aplicação Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: f8a9025a50b2815f0e6030e7baf317b261c8c462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86256337"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Configurar e configurar procuração inversa no Tecido de Serviço Azure
O Reverse Proxy é um serviço opcional Azure Service Fabric que ajuda microserviços a funcionar num cluster de Tecidos de Serviço a descobrir e comunicar com outros serviços que têm pontos finais http. Para saber mais, consulte [Reverse proxy in Azure Service Fabric](service-fabric-reverseproxy.md). Este artigo mostra-lhe como configurar e configurar o proxy inverso no seu cluster. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Ativar procuração inversa usando o portal Azure

O portal Azure oferece uma opção para ativar o proxy invertido quando cria um novo cluster de Tecido de Serviço. Não é possível atualizar um cluster existente para usar o proxy invertido através do portal. 

Para configurar o representante inverso quando criar um cluster utilizando o [portal Azure,](./service-fabric-cluster-creation-via-portal.md)certifique-se de que faz o seguinte:

1. No **Passo 2: Configuração do cluster**, na **configuração do tipo nó**, selecione **Ativar o proxy invertido**.

   ![Ativar procuração inversa no portal](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Opcional) Para configurar um representante inverso seguro, é necessário configurar um certificado TLS/SSL. No **passo 3: Segurança**, nas **definições de segurança do cluster configure,** sob **o tipo de configuração**, selecione **Custom**. Em seguida, no **certificado Reverse Proxy SSL**, selecione **Inclua um certificado SSL para procuração inversa** e introduza os dados do certificado.

   ![Configurar proxy de reverter seguro no portal](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Se optar por não configurar o representante inverso com um certificado quando criar o cluster, pode fazê-lo mais tarde através do modelo de Gestor de Recursos para o grupo de recursos do cluster. Para saber mais, consulte [Ativar o proxy invertido através dos modelos Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Ativar procuração inversa através de modelos de Gestor de Recursos Azure

Para clusters em Azure, pode utilizar o modelo Azure Resource Manager para ativar o proxy inverso no Tecido de Serviço. Pode ativar o proxy invertido quando criar o cluster ou ative-o atualizando o cluster mais tarde. 

Para um novo cluster, pode [criar um modelo personalizado de Gestor de Recursos](service-fabric-cluster-creation-via-arm.md) ou pode usar um modelo de amostra. 

Pode encontrar modelos de gestor de recursos de amostra que podem ajudá-lo a configurar um proxy seguro para um cluster Azure nos [modelos de amostra de procuração de proxy de reverso seguro](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) no GitHub. Consulte o [Configure HTTPS Reverse Proxy num cluster seguro](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) no ficheiro README para obter instruções e os modelos a utilizar para configurar um representante de reverso seguro com um certificado e para lidar com a capotagem do certificado.

Para um cluster existente, pode exportar o modelo de Gestor de Recursos para o grupo de recursos do cluster utilizando o [portal Azure](../azure-resource-manager/templates/export-template-portal.md), [PowerShell,](../azure-resource-manager/management/manage-resources-powershell.md)ou o [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md).

Depois de ter um modelo de Gestor de Recursos, pode ativar o representante inverso com os seguintes passos:

1. Defina uma porta para o representante inverso na [secção Parâmetros](../azure-resource-manager/templates/template-syntax.md) do modelo.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especificar a porta para cada um dos objetos de nótype na secção tipo [de recurso](../azure-resource-manager/templates/template-syntax.md) [**Microsoft.ServiceFabric/clusters**](/azure/templates/microsoft.servicefabric/clusters) .

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
3. Para configurar os certificados TLS/SSL na porta para o representante inverso, adicione o certificado à propriedade ***reverseProxyCertificate** _ na secção _ *Microsoft.ServiceFabric/clusters* *  [Tipo de recurso](../azure-resource-manager/templates/template-syntax.md).

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
 Se o certificado de procuração inversa for diferente do certificado que assegura o cluster, então o certificado previamente especificado deve ser instalado na máquina virtual e adicionado à lista de controlo de acesso (ACL) para que o Service Fabric possa aceder ao mesmo. Isto pode ser feito na secção tipo [de recurso](../azure-resource-manager/templates/template-syntax.md) [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets) . Para instalação, adicione o certificado ao sistema operativo os. A secção de extensão do modelo pode atualizar o certificado na ACL.

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
> Quando utilizar certificados diferentes do certificado de cluster para ativar o representante inverso num cluster existente, instale o certificado de procuração inversa e atualize o ACL no cluster antes de ativar o proxy inverso. Preencha a implementação do [modelo do Gestor de Recursos Azure](service-fabric-cluster-creation-via-arm.md) utilizando as definições mencionadas anteriormente antes de iniciar uma implementação para ativar o proxy invertido nos passos 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Ativar procuração inversa em clusters autónomos

Para agrupamentos autónomos, ativar o representante inverso no ClusterConfig.jsficheiro. Pode ativar o proxy invertido na criação do cluster ou atualizando a configuração para um cluster existente. Para saber mais sobre as definições disponíveis em ClusterConfig.jsem ficheiros, consulte [as definições do cluster Autónomo](./service-fabric-cluster-manifest.md).

Os passos seguintes mostram-lhe as definições a utilizar para ativar o proxy invertido e, opcionalmente, para proteger o representante inverso com um certificado X.509. 

1. Para ativar o proxy invertido, desafie o valor **reversoProxyEndpointPort** para o tipo de nó sob **propriedades** no cluster config. O seguinte JSON mostra a definição da porta de ponto final de procuração inversa para 19081 para os nóles com um tipo de "NodeType0":

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
2. (Opcional) Para um representante inverso seguro, configure um certificado na secção de **segurança** sob **propriedades**. 
   - Para um ambiente de desenvolvimento ou teste, pode utilizar a **definição ReverseProxyCertificate:**

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
   - Para um ambiente de produção, recomenda-se a definição **reverseProxyCertificateCommonNames:**

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

   Para saber mais sobre a configuração e gestão de certificados para um cluster autónomo, bem como mais detalhes sobre a configuração de certificados utilizados para garantir a procuração inversa, consulte [a segurança baseada em certificados X509](./service-fabric-windows-cluster-x509-security.md).

Depois de modificar a sua ClusterConfig.jsno ficheiro para ativar o proxy invertido, siga as instruções na [atualização da configuração do cluster](service-fabric-cluster-config-upgrade-windows-server.md) para empurrar as alterações para o seu cluster.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Expor procuração inversa em uma porta pública através do Equilibrador de Carga Azure

Para abordar o representante inverso de fora de um cluster Azure, crie as regras do Azure Load Balancer e uma Sonda Azure Health para a porta de procuração inversa. Estes passos podem ser realizados usando o portal Azure ou o modelo de Gestor de Recursos a qualquer momento depois de ter criado o cluster. 

> [!WARNING]
> Quando configurar a porta do proxy invertido no Balanceador de Carga, todos os microserviços do cluster que expõem um ponto final HTTP são endereçais de fora do cluster. Isto significa que os microserviços destinados a serem internos podem ser detetáveis por um determinado utilizador malicioso. Isto apresenta potencialmente vulnerabilidades graves que podem ser exploradas; Por exemplo:
>
> * Um utilizador mal-intencionado pode lançar um ataque de negação de serviço chamando repetidamente um serviço interno que não tenha uma superfície de ataque suficientemente endurecida.
> * Um utilizador malicioso pode entregar pacotes mal formados a um serviço interno, resultando em comportamento não intencional.
> * Um serviço destinado a ser interno pode devolver informações privadas ou sensíveis que não se destinem a ser expostas a serviços fora do cluster, expondo assim esta informação sensível a um utilizador mal-intencionado. 
>
> Certifique-se de compreender e mitigar completamente as potenciais ramificações de segurança para o seu cluster e as aplicações em execução nele, antes de tornar público o porto de procuração inversa. 
>

Se quiser expor publicamente o proxy invertido para um cluster autónomo, a forma como o faz dependerá do sistema que hospeda o cluster e está fora do âmbito deste artigo. No entanto, o aviso anterior sobre a exposição publicamente de procuração inversa, no entanto, ainda se aplica.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Expor o representante inverso usando o portal Azure 

1. No portal Azure, clique no grupo de recursos para o seu cluster e, em seguida, clique no equilibrador de carga para o seu cluster.
2. Para adicionar uma sonda de saúde para a porta de procuração inversa, no painel esquerdo da janela do balançador de carga, em **DEFINIÇÕES,** clique nas **sondas Health**. Em seguida, clique em **Adicionar** na parte superior da janela das sondas Health e introduzir detalhes para a porta de procuração inversa e, em seguida, clique em **OK**. Por predefinição, a porta de procuração inversa é 19081, a menos que a tenha alterado quando criou o cluster.

   ![Configure sonda de saúde por procuração inversa](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Para adicionar uma regra do Balanceador de Carga para expor a porta de procuração inversa, no painel esquerdo da janela do balançador de carga, em **DEFINIÇÕES,** clique em **Regras de equilíbrio de carga**. Em seguida, clique em **Adicionar** na parte superior da janela de regras de equilíbrio de carga e introduzir detalhes para a porta de procuração inversa. Certifique-se de que define o valor **do Porto** para a porta onde pretende que o representante inverso seja exposto, o valor da **porta Backend** para a porta que definiu quando ativou o proxy invertido, e o valor da **sonda Health** para a sonda de saúde que configuraste no passo anterior. Desa parte de outros campos conforme apropriado e clique **em OK**.

   ![Regra do balançador de carga configurar para procuração inversa](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Expor o proxy invertido através de modelos de Gestor de Recursos

O JSON seguinte refere o mesmo modelo que é usado em [Enable reverse proxy via Azure Resource Manager modelos](#enable-reverse-proxy-via-azure-resource-manager-templates). Consulte essa secção do documento para obter informações sobre como criar um modelo de Gestor de Recursos ou exportar um modelo para um cluster existente.  As alterações são efetuadas na secção tipo [de recurso](../azure-resource-manager/templates/template-syntax.md) [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers) .

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


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Personalize o comportamento de procuração inversa usando configurações de tecido

Pode personalizar o comportamento de procuração inversa através de definições de tecido no modelo de Gestor de Recursos para clusters alojados em Azure ou no ClusterConfig.jsem ficheiro para clusters autónomos. As definições que controlam o comportamento de procuração inversa estão localizadas na secção [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) na secção **fabricSettings** sob a secção **de propriedades** do cluster. 

Por exemplo, pode definir o valor do **DefaultHttpRequestTimeout** para definir o tempo limite de pedidos para o representante inverso para 180 segundos como no seguinte JSON:

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

Para obter mais informações sobre a atualização das definições de tecido para clusters Azure, consulte [configurações de clusters personalizadas utilizando modelos de Gestor de Recursos](service-fabric-cluster-config-upgrade-azure.md). Para agrupamentos autónomos, consulte [configurações de clusters personalizadas para clusters autónomos](service-fabric-cluster-config-upgrade-windows-server.md). 

Várias configurações de tecido são usadas para ajudar a estabelecer uma comunicação segura entre procuração inversa e serviços. Para obter informações detalhadas sobre estas definições, consulte [Connect a um serviço seguro com o representante inverso](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Passos seguintes
* [Configurar o encaminhamento para garantir o serviço HTTP com o proxy invertido](service-fabric-reverseproxy-configure-secure-communication.md)
* Para opções de configuração de procuração inversa, consulte [a secção ApplicationGateway/Http nas definições de cluster de tecido de serviço personalizado](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
