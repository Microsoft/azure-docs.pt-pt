---
title: Mova um equilibrador de carga externa Azure para outra região do Azure utilizando o portal Azure
description: Utilize um modelo de Gestor de Recursos Azure para mover um equilibrador de carga externo de uma região do Azure para outra utilizando o portal Azure.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638532"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Mova um equilibrador de carga externo para outra região utilizando o portal Azure

Há vários cenários em que se gostaria de mover um equilibrista de carga externa de uma região para outra. Por exemplo, é melhor criar outro equilibrante de carga externo com a mesma configuração para testes. Também é possível que queira mover um equilibrador de carga externo para outra região como parte do planeamento da recuperação de desastres.

Num sentido literal, não se pode mover um equilibrador de carga externa Azure de uma região para outra. Mas pode utilizar um modelo do Gestor de Recursos Azure para exportar a configuração existente e endereço IP público de um equilibrador de carga externo. Em seguida, pode encenar o recurso noutra região exportando o equilibrante de carga e IP público para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantando o modelo para a nova região. Para obter mais informações sobre o Gestor de Recursos e modelos, consulte grupos de [recursos de exportação para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o equilibrador de carga externa Azure está na região de Azure, a partir da qual pretende mover-se.

- Os equilibradores de carga externos azure não podem ser movidos entre regiões. Terá de associar o novo equilibrador de carga aos recursos na região alvo.

- Para exportar uma configuração de equilíbrio de carga externa e implementar um modelo para criar um equilibrador de carga externo noutra região, terá de ser atribuído ao papel de Colaborador de Rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede, iPs públicos e redes virtuais.

- Verifique se a sua subscrição Azure permite criar equilibradores de carga externos na região alvo. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição dos equilibradores de carga. Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Preparar e mover
Os seguintes procedimentos mostram como preparar o equilibrador de carga externo para a mudança utilizando um modelo de Gestor de Recursos e mover a configuração do equilíbrio de carga externa para a região alvo utilizando o portal Azure. Primeiro deve exportar a configuração ip pública do equilibrador de carga externo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Exportar o modelo público de IP e implementar o IP público a partir do portal

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.
2. Localize o grupo de recursos que contém a fonte de IP público e selecione-o.
3. Selecione modelo**de exportação** **de configurações** > .
4. Selecione **Implementar** sob o modelo de **exportação**.
5. Selecione **MODELO** > **Editar parâmetros** para abrir o ficheiro parâmetros.json no editor online.
8. Para editar o parâmetro do nome IP público, altere o **valor** do imóvel em **parâmetros** do nome IP público de origem para o nome do seu IP público alvo. Encerre o nome em aspas.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Selecione **Guardar** no editor.

9.  Selecione modelo de**edição** de **MODELO** > para abrir o ficheiro template.json no editor online.

10. Para editar a região-alvo para a qual o IP público será movido, altere a propriedade de **localização** sob **recursos:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    Para obter códigos de localização da região, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços. Por exemplo, o código para os EUA Centrais é **central.**
    
12. Também pode alterar outros parâmetros no modelo se quiser ou precisar, dependendo dos seus requisitos:

    * **SKU.** Pode alterar o SKU do IP público na configuração de padrão para básico ou de básico para padrão, alterando a propriedade de **nome** sob **sku** no ficheiro template.json:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Para obter informações sobre as diferenças entre iPs públicos básicos e standard, consulte [Criar, alterar ou eliminar um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Método público** de atribuição de IP e tempo limite de **inativo.** Pode alterar o método público de atribuição de IP alterando a propriedade **publicIPAllocationMethod** de **Dynamic** para **Static** ou de **Static** to **Dynamic**. Pode alterar o tempo inativo alterando a propriedade **idleTimeoutInMinutes** para o valor desejado. O padrão é **de 4**.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Para obter informações sobre os métodos de atribuição e os valores de tempo inativo, consulte [Criar, alterar ou eliminar um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. Selecione **Guardar** no editor online.

14. Selecione**Subscrição** **BASICS** > para escolher a subscrição onde será implementado o IP público-alvo.

15. Selecione **basics** > **Resource group** para escolher o grupo de recursos onde o IP público alvo será implantado. Pode selecionar **Criar novos** para criar um novo grupo de recursos para o IP público alvo. Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do IP público de origem existente.

16. Verifique se a**localização** **BASICS** > está definida para o local alvo onde pretende que o IP público seja implantado.

17. Em **DEFINIÇÕES,** verifique se o nome corresponde ao nome que inseriu anteriormente no editor de parâmetros.

18. Selecione a caixa de verificação **TERMOS E CONDIÇÕES.**

19. Selecione **Comprar** para implementar o IP público alvo.

20. Se tiver outro IP público que está a ser utilizado para o NAT de saída para o equilíbrio de carga que está a ser movido, repita os passos anteriores para exportar e implante o segundo IP público de saída para a região alvo.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Exportar o modelo de equilíbrio de carga externa e implementar o equilibrador de carga do portal Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.
2. Localize o grupo de recursos que contém o equilibrador de carga externo fonte e selecione-o.
3. Selecione modelo**de exportação** **de configurações** > .
4. Selecione **Implementar** sob o modelo de **exportação**.
5. Selecione **MODELO** > **Editar parâmetros** para abrir o ficheiro parâmetros.json no editor online.

5. Para editar o parâmetro do nome do equilibrador de carga externo, altere a propriedade de **valor** do nome do equilibrador de carga externo de origem para o nome do seu equilibrador de carga externo alvo. Encerre o nome em aspas.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Para editar o valor do IP público-alvo que moveu nos passos anteriores, tem primeiro de obter o ID de recurso e, em seguida, colá-lo no ficheiro parâmetros.json. Para obter a identificação:

    1. Em outro separador ou janela do navegador, inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Grupos de Recursos**.
    2. Localize o grupo de recursos alvo que contém o IP público que moveu nos passos anteriores. Selecione-o.
    3. Selecione**Propriedades de** **Definições** > .
    4. Na lâmina à direita, realce o ID do **recurso** e copie-o para a área de reparação. Em alternativa, pode selecionar **cópia para refazer** à direita do caminho de ID de **recurso.**
    5. Colar o ID de recurso na propriedade de **valor** no editor **de Edição Parametros** que está aberto na outra janela ou separador do navegador:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Selecione **Guardar** no editor online.


7.  Se configurar regras de saída na NAT e de saída para o equilibrador de carga, verá uma terceira entrada neste ficheiro para a identificação externa do IP público de saída. Repita os passos anteriores na **região alvo** para obter o ID para o IP público de saída. Colhe a identificação no ficheiro parâmetros.json:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Selecione modelo de**edição** de **MODELO** > para abrir o ficheiro template.json no editor online.
9.  Para editar a região-alvo para a qual será movida a configuração do equilíbrio de carga externa, altere a propriedade de **localização** sob **recursos** no ficheiro template.json:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Para obter códigos de localização da região, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços. Por exemplo, o código para os EUA Centrais é **central.**

11. Também pode alterar outros parâmetros no modelo se quiser ou precisar, dependendo dos seus requisitos:

    * **SKU.** Pode alterar o SKU do equilibrador de carga externo na configuração de padrão para básico ou de básico para padrão, alterando a propriedade de **nome** sob **sku** no ficheiro template.json:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Para obter informações sobre as diferenças entre os equilibradores de carga SKU básicos e standard, consulte a visão geral do [Equilíbrio de Carga Padrão Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

    * **Regras de equilíbrio de carga.** Pode adicionar ou remover regras de equilíbrio de carga na configuração adicionando ou removendo entradas na secção **loadBalancingRules** do ficheiro template.json:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Para obter informações sobre as regras de equilíbrio de carga, consulte [o que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondas.** Pode adicionar ou remover uma sonda para o equilibrador de carga na configuração adicionando ou removendo entradas na secção de **sondas** do ficheiro template.json:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Para mais informações, consulte as sondas de [saúde Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Regras na nat de entrada.** Pode adicionar ou remover as regras nat de entrada para o equilibrador de carga adicionando ou removendo entradas na secção **inboundNatRules** do ficheiro template.json:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Para completar a adição ou remoção de uma regra NAT de entrada, a regra deve ser presente ou removida como uma propriedade **tipo** no final do ficheiro template.json:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Para obter informações sobre as regras de entrada na NAT, veja [o que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Regras de saída.** Pode adicionar ou remover regras de saída na configuração editando a propriedade **outboundRules** no ficheiro template.json:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Para mais informações, consulte as regras de [saída do Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. Selecione **Guardar** no editor online.

13. Selecione**Subscrição** **BASICS** > para escolher a subscrição onde o equilibrador de carga externa alvo será implantado.

15. Selecione o grupo **BASICS** > **Resource** para escolher o grupo de recursos onde o equilibrador de carga-alvo será implantado. Pode selecionar **Criar novos** para criar um novo grupo de recursos para o equilibrador de carga externa alvo. Ou pode escolher o grupo de recursos existente que criou anteriormente para o IP público. Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do equilibrador de carga externa fonte existente.

16. Verifique se a**localização** **BASICS** > está definida para o local alvo onde pretende que o equilibrista de carga externo seja implantado.

17. Em **DEFINIÇÕES,** verifique se o nome corresponde ao nome que inseriu anteriormente no editor de parâmetros. Verifique se os IDs de recursos são povoados para quaisquer IPs públicos na configuração.

18. Selecione a caixa de verificação **TERMOS E CONDIÇÕES.**

19. Selecione **Comprar** para implementar o IP público alvo.

## <a name="discard"></a>Eliminar

Se pretender descartar o IP público-alvo e o equilibrador de carga externo, elimine o grupo de recursos que os contém. Para isso, selecione o grupo de recursos do seu painel de instrumentos no portal e, em seguida, selecione **Eliminar** na parte superior da página de visão geral.

## <a name="clean-up"></a>Limpeza

Para comprometer as alterações e completar a mudança do PI público e do equilibrador de carga externo, elimine a fonte de IP público e o equilibrador de carga externo ou grupo de recursos. Para tal, selecione esse grupo de recursos do seu painel de instrumentos no portal e, em seguida, selecione **Eliminar** na parte superior de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um equilibrador de carga externa Azure de uma região para outra e limpou os recursos de origem. Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
