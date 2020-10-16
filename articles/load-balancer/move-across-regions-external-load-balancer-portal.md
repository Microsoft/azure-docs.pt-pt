---
title: Mover um equilibrador de carga externo Azure para outra região do Azure utilizando o portal Azure
description: Utilize um modelo de Gestor de Recursos Azure para mover um equilibrador de carga externo de uma região Azure para outra utilizando o portal Azure.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 0598f21cddbaeef6b3cd10cd77250eeae8bd34bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808715"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Mover um equilibrador de carga externo para outra região utilizando o portal Azure

Há vários cenários em que se quer mover um equilibrador de carga externo de uma região para outra. Por exemplo, é melhor criar outro equilibrador de carga externo com a mesma configuração para testes. Também é possível que queira mover um equilibrador de carga externo para outra região como parte do planeamento da recuperação de desastres.

Num sentido literal, não se pode mover um equilibrador externo Azure de uma região para outra. Mas pode utilizar um modelo de Gestor de Recursos Azure para exportar a configuração existente e o endereço IP público de um equilibrador de carga externo. Em seguida, pode errá-lo noutra região exportando o equilibrador de carga e o IP público para um modelo, modificando os parâmetros para combinar com a região de destino e, em seguida, implantando o modelo para a nova região. Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [os grupos de recursos de exportação para os modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o balançador de carga externo Azure se encontra na região de Azure, de onde pretende mover-se.

- Os equilibradores de carga externos não podem ser movidos entre regiões. Terá de associar o novo equilibrador de carga aos recursos na região alvo.

- Para exportar uma configuração do balanceador de carga externa e implementar um modelo para criar um balanceador de carga externo noutra região, terá de ser atribuído o papel de Contribuinte de Rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que está a usar. Este layout inclui, mas não se limita a equilibrar cargas, grupos de segurança de rede, IPs públicos e redes virtuais.

- Verifique se a sua subscrição Azure permite criar equilibradores de carga externos na região alvo. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição dos equiliblos de carga. Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Preparar e mover
Os seguintes procedimentos mostram como preparar o balançador de carga externo para o movimento utilizando um modelo de Gestor de Recursos e mover a configuração do balançador de carga externo para a região alvo utilizando o portal Azure. Primeiro, deve exportar a configuração IP pública do equilibrador de carga externo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Exportar o modelo de IP público e implementar o IP público a partir do portal

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.
2. Localize o grupo de recursos que contém o IP público de origem e selecione-o.
3. Selecione **Settings**  >  **o modelo de exportação de**definições.
4. Selecione **Implementar** sob **o modelo de Exportação.**
5. Selecione **TEMPLATE**  >  **modelo Editar parâmetros** para abrir o parameters.jsno ficheiro no editor online.
8. Para editar o parâmetro do nome IP público, altere a propriedade de **valor** em **parâmetros** do nome IP público de origem para o nome do seu IP público alvo. Incluir o nome em aspas.

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

    **Selecione Save** no editor.

9.  Selecione **modelo de**edição de modelo para abrir o template.jsno ficheiro no editor  >  **Edit template** online.

10. Para editar a região-alvo para a qual o IP público será movido, altere o imóvel de **localização** em **recursos**:

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
  
    Para obter códigos de localização da região, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços. Por exemplo, o código para os EUA centrais é **central.**
    
12. Também pode alterar outros parâmetros no modelo se quiser ou precisar, dependendo dos seus requisitos:

    * **SKU.** Pode alterar o SKU do IP público na configuração de padrão para básico ou de base para padrão, alterando a propriedade do **nome** sob **sku** no template.jsno ficheiro:

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

        Para obter informações sobre as diferenças entre os IPs públicos SKU básicos e padrão, consulte [Criar, alterar ou eliminar um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Método de atribuição de IP público** e tempo limite de marcha **lenta**. Pode alterar o método de atribuição de IP público alterando a propriedade **publicIPAllocationMethod** de **Dynamic** para **Static** ou de **Estática** para **Dinâmica.** Pode alterar o tempo limite de marcha lenta, alterando a propriedade **idleTimeoutInMinutes** para o valor pretendido. O padrão é **4**.

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

        Para obter informações sobre os métodos de atribuição e os valores de tempo limite inativo, consulte [Criar, alterar ou eliminar um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. **Selecione Save** no editor online.

14. Selecione **SUBSCRIÇão BASICS**  >  **Subscription** para escolher a subscrição onde o IP público-alvo será implementado.

15. Selecione o grupo de recursos **BASICS**  >  **Resource group** para escolher o grupo de recursos onde o IP público-alvo será implementado. Pode selecionar **Criar novo** para criar um novo grupo de recursos para o IP público-alvo. Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do IP público de origem existente.

16. Verifique se **a localização BASICS**  >  **Location** está definida para o local alvo onde deseja que o IP público seja implantado.

17. Em **DEFINIÇÕES,** verifique se o nome corresponde ao nome que inseriu anteriormente no editor de parâmetros.

18. Selecione a caixa de verificação **termos e condições.**

19. Selecione **Comprar** para implementar o IP público alvo.

20. Se tiver outro IP público que está a ser usado para o NAT de saída para o balanceador de carga que está a ser movido, repita os passos anteriores para exportar e coloque o segundo IP público de saída para a região alvo.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Exportar o modelo de balançador de carga externo e implementar o equilibrador de carga a partir do portal Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.
2. Localize o grupo de recursos que contém o balanceador de carga externo de origem e selecione-o.
3. Selecione **Settings**  >  **o modelo de exportação de**definições.
4. Selecione **Implementar** sob **o modelo de Exportação.**
5. Selecione **TEMPLATE**  >  **modelo Editar parâmetros** para abrir o parameters.jsno ficheiro no editor online.

5. Para editar o parâmetro do nome do balançador de carga externo, altere a propriedade de **valor** do nome do balançador externo de origem para o nome do equilibrador de carga externo alvo. Incluir o nome em aspas.

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

6.  Para editar o valor do IP público-alvo que moveu nos passos anteriores, deve primeiro obter o ID do recurso e, em seguida, colá-lo no parameters.jsem ficheiro. Para obter o ID:

    1. Em outro separador ou janela do navegador, inscreva-se no [portal Azure](https://portal.azure.com) e selecione **grupos de Recursos.**
    2. Localize o grupo de recursos-alvo que contém o IP público que moveu nos passos anteriores. Selecione-a.
    3. Selecione **Configurações** > **Propriedades**.
    4. Na lâmina à direita, realce o **ID do recurso** e copie-o para a área de transferência. Em alternativa, pode selecionar **a cópia para a área de transferência** à direita do caminho de ID de **recursos.**
    5. Cole o ID do recurso na propriedade de **valor** no editor **editar parâmetros** que está aberto na outra janela ou separador do navegador:

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
    6. **Selecione Save** no editor online.


7.  Se configurar as regras de saída da NAT e de saída para o equilibrador de carga, verá uma terceira entrada neste ficheiro para o ID externo do IP público de saída. Repita os passos anteriores na **região alvo** para obter o ID para o IP público de saída. Cole essa iD no parameters.jsarquivado:

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

8.  Selecione **modelo de**edição de modelo para abrir o template.jsno ficheiro no editor  >  **Edit template** online.
9.  Para editar a região-alvo para a qual será movida a configuração do balançador de carga externo, altere a propriedade de **localização** sob **recursos** na template.jsem arquivo:

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

10. Para obter códigos de localização da região, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços. Por exemplo, o código para os EUA centrais é **central.**

11. Também pode alterar outros parâmetros no modelo se quiser ou precisar, dependendo dos seus requisitos:

    * **SKU.** Pode alterar o SKU do balançador de carga externo na configuração de padrão para básico ou de base para padrão, alterando a propriedade do **nome** sob **sku** no template.jsno ficheiro:

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
      Para obter informações sobre as diferenças entre os balançadores de carga SKU básicos e padrão, consulte [a visão geral do Balanço de Carga Padrão Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

    * **Regras de equilíbrio de carga**. Pode adicionar ou remover as regras de equilíbrio de carga na configuração adicionando ou removendo entradas na secção **loadBalancingRules** do template.jsno ficheiro:

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
       Para obter informações sobre regras de equilíbrio de carga, veja [o que é o Equilibrador de Carga Azure?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondas.** Pode adicionar ou remover uma sonda para o equilibrador de carga na configuração adicionando ou removendo entradas na secção de **sondas** da template.jsno ficheiro:

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
       Para obter mais informações, consulte [as sondas de saúde Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Regras NAT de entrada**. Pode adicionar ou remover as regras NAT de entrada para o balançador de carga adicionando ou removendo entradas na secção **de inboundNatRules** do template.jsno ficheiro:

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
        Para completar a adição ou remoção de uma regra NAT de entrada, a regra deve ser presente ou removida como uma propriedade **tipo** no final do template.jsem arquivo:

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
        Para obter informações sobre as regras NAT de entrada, consulte [o que é o Equilibrador de Carga Azure?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Regras de saída.** Pode adicionar ou remover as regras de saída na configuração editando a propriedade **outboundRules** no template.jsno ficheiro:

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

         Para obter mais informações, consulte [as regras de saída do Balancer de Carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. **Selecione Save** no editor online.

13. Selecione **SUBSCRIÇão BASICS**  >  **Subscription** para escolher a subscrição onde o balanceador de carga externo alvo será implantado.

15. Selecione o grupo de recursos **BASICS**  >  **Resource group** para escolher o grupo de recursos onde o balanceador de carga-alvo será implantado. Pode selecionar **Criar novo** para criar um novo grupo de recursos para o balanceador de carga externo alvo. Ou pode escolher o grupo de recursos existente que criou anteriormente para o IP público. Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do equilibrador de carga externo de origem existente.

16. Verifique se **a localização BASICS**  >  **Location** está definida para o local alvo onde deseja que o balançador de carga externo seja implantado.

17. Em **DEFINIÇÕES,** verifique se o nome corresponde ao nome introduzido anteriormente no editor de parâmetros. Verifique se os IDs de recursos são preenchidos para quaisquer IPs públicos na configuração.

18. Selecione a caixa de verificação **termos e condições.**

19. Selecione **Comprar** para implementar o IP público alvo.

## <a name="discard"></a>Eliminar

Se pretender descartar o IP público-alvo e o equilibrador de carga externo, elimine o grupo de recursos que os contém. Para tal, selecione o grupo de recursos do seu painel de instrumentos no portal e, em seguida, **selecione Eliminar** no topo da página geral.

## <a name="clean-up"></a>Limpeza

Para comprometer as alterações e completar o movimento do equilibrador de carga público e externo, elimine a fonte de IP público e o equilibrador de carga externo ou grupo de recursos. Para tal, selecione o grupo de recursos do seu painel de instrumentos no portal e, em seguida, **selecione Delete** no topo de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu um equilibrador de carga externa Azure de uma região para outra e limpou os recursos de origem. Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou subscrição](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
