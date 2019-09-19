---
title: Mover o Load Balancer externo do Azure para outra região do Azure usando o portal do Azure
description: Use Azure Resource Manager modelo para mover o Load Balancer externo do Azure de uma região do Azure para outra usando o portal do Azure.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: eda0d6e8fe56b985c3b29fa80cee880444d63741
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105296"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-the-azure-portal"></a>Mover o Load Balancer externo do Azure para outra região usando o portal do Azure

Há vários cenários em que você deseja mover seu balanceador externo de carga existente de uma região para outra. Por exemplo, talvez você queira criar um balanceador de carga externo com a mesma configuração para teste. Você também pode querer mover um balanceador externo de carga para outra região como parte do planejamento de recuperação de desastre.

Os balanceadores de carga externos do Azure não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo de Azure Resource Manager para exportar a configuração existente e o IP público de um balanceador de carga externo.  Em seguida, você pode preparar o recurso em outra região exportando o balanceador de carga e o IP público para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar os modelos na nova região.  Para obter mais informações sobre o Gerenciador de recursos e modelos, consulte [Exportar grupos de recursos para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o balanceador de carga externo do Azure está na região do Azure da qual você deseja mover.

- Os balanceadores de carga externos do Azure não podem ser movidos entre regiões.  Você precisará associar o novo balanceador de carga aos recursos na região de destino.

- Para exportar uma configuração de balanceador de carga externa e implantar um modelo para criar um balanceador de carga externo em outra região, você precisará da função de colaborador de rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que você está usando no momento. Esse layout inclui, mas não se limita a balanceadores de carga, grupos de segurança de rede, IPs públicos e redes virtuais.

- Verifique se sua assinatura do Azure permite criar balanceadores de carga externos na região de destino usada. Contacte o suporte para ativar a quota necessária.

- Verifique se sua assinatura tem recursos suficientes para dar suporte à adição de balanceadores de carga para esse processo.  Confira [assinatura do Azure e limites de serviço, cotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar o balanceador externo de carga para a movimentação usando um modelo do Resource Manager e mover a configuração do balanceador de carga externo para a região de destino usando o portal do Azure.  Como parte desse processo, a configuração de IP público do balanceador de carga externo deve ser incluída e deve ser feita primeiro antes de mover o balanceador de carga externo.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-the-portal"></a>Exportar o modelo de IP público e implantá-lo por meio do portal

1. Faça logon no [portal do Azure](http://portal.azure.com) > **grupos de recursos**.
2. Localize o grupo de recursos que contém o IP público de origem e clique nele.
3. Selecione **configurações** > de >**modelo de exportação**.
4. Escolha **implantar** na folha **Exportar modelo** .
5. Clique em **modelo** > **Editar parâmetros** para abrir o arquivo **Parameters. JSON** no editor online.
8. Para editar o parâmetro do nome do IP público, altere a propriedade em**valor** de **parâmetros** > do nome IP público de origem para o nome do seu IP público de destino, verifique se o nome está entre aspas:

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

    Clique em **salvar** no editor.

9.  Clique em **modelo** > **Editar modelo** para abrir o arquivo **Template. JSON** no editor online. 

10. Para editar a região de destino em que o IP público será movido, altere a propriedade **local** em **recursos**:

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
  
11. Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central** = **centralus**.
    
12. Você também pode alterar outros parâmetros no modelo se escolher e forem opcionais, dependendo dos seus requisitos:

    * **SKU** -você pode alterar a SKU do IP público na configuração de Standard para básico ou básico para Standard alterando a propriedade**nome** do **SKU** > no arquivo **Template. JSON** :

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

        Para obter mais informações sobre as diferenças entre IPS públicos do SKU básico e Standard, consulte [criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Método de alocação de IP público** e **tempo limite de ociosidade** -você pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **dinâmico** para **estático** ou **estático** para **dinâmico** . O tempo limite de ociosidade pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para o valor desejado.  O padrão é **4**:

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

        Para obter mais informações sobre os métodos de alocação e os valores de tempo limite de ociosidade, consulte [criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. Clique em **salvar** no editor online.

14. Clique em**assinatura** **básica** > para escolher a assinatura na qual o IP público de destino será implantado.

15. Clique em**grupo de recursos** **básico** > para escolher o grupo de recursos em que o IP público de destino será implantado.  Você pode clicar em **criar novo** para criar um novo grupo de recursos para o IP público de destino.  Verifique se o nome não é o mesmo que o grupo de recursos de origem do IP público de origem existente. 

16. Verifique se o**local** **básico** > está definido como o local de destino onde você deseja que o IP público seja implantado.

17. Verifique em **configurações** que o nome corresponde ao nome que você inseriu no editor de parâmetros acima.

18. Marque a caixa em **termos e condições**.

19. Clique no botão **comprar** para implantar o IP público de destino.
20. Se você tiver outro IP público que está sendo usado para NAT de saída para o balanceador de carga que está sendo movido, repita as etapas acima para exportar e implantar o segundo IP público de saída na região de destino.

### <a name="export-the-external-load-balancer-template-and-deploy-from-the-azure-portal"></a>Exportar o modelo de balanceador de carga externo e implantar do portal do Azure

1. Faça logon no [portal do Azure](http://portal.azure.com) > **grupos de recursos**.
2. Localize o grupo de recursos que contém o balanceador de carga externo de origem e clique nele.
3. Selecione **configurações** > de >**modelo de exportação**.
4. Escolha **implantar** na folha **Exportar modelo** .
5. Clique em **modelo** > **Editar parâmetros** para abrir o arquivo **Parameters. JSON** no editor online.

5. Para editar o parâmetro do nome do balanceador de carga externo, altere o **valor** da Propriedade do nome do balanceador de carga externo de origem para o nome do balanceador de carga externo de destino, verifique se o nome está entre aspas:

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

6.  Para editar o valor do IP público de destino que foi movido acima, você deve primeiro obter a ID do recurso e, em seguida, copiá-la e colá-la no arquivo **Parameters. JSON** . Para obter a ID:
    
    1. Faça logon no [portal do Azure](http://portal.azure.com) > **grupos de recursos** em outra guia ou janela do navegador.
    2. Localize o grupo de recursos de destino que contém o IP público movido das etapas acima e clique nele.
    3. Selecione **configurações** > de >**Propriedades**.
    4. Na folha à direita, realce a **ID do recurso** e copie-a para a área de transferência.  Como alternativa, você pode clicar no botão **copiar para a área de transferência** à direita do caminho da **ID de recurso** .
    5. Cole a ID do recurso na propriedade **Value** no editor de **Editar parâmetros** aberto na outra janela do navegador ou na guia:

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
    6. Clique em **salvar** no editor online.
   

7.  Se você tiver configurado o NAT de saída e as regras de saída para o balanceador de carga, uma terceira entrada estará presente nesse arquivo para a ID externa para o IP público de saída.  Repita as etapas acima na **região de destino** para obter a ID do IP público de saída e cole essa entrada no arquivo **Parameters. JSON** :

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

8.  Clique em **modelo** > **Editar modelo** para abrir o arquivo **Template. JSON** no editor online.
9.  Para editar a região de destino em que a configuração do balanceador de carga externo será movida, altere a propriedade **local** em **recursos** no arquivo **Template. JSON** :

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

10. Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central** = **centralus**.

11. Você também pode alterar outros parâmetros no modelo se escolher e forem opcionais, dependendo dos seus requisitos:
    
    * **SKU** -você pode alterar a SKU do balanceador externo de carga na configuração de Standard para básico ou básico para Standard alterando a propriedade**nome** do **SKU** > no arquivo **Template. JSON** :

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
      Para obter mais informações sobre as diferenças entre os balanceadores de carga do SKU básico e Standard, consulte [visão geral do Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Regras de balanceamento de carga** – você pode adicionar ou remover regras de balanceamento de carga na configuração adicionando ou removendo entradas na seção **loadBalancingRules** do arquivo **Template. JSON** :

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
       Para obter mais informações sobre regras de balanceamento de carga, consulte [o que é Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Investigações** – você pode adicionar ou remover uma investigação para o balanceador de carga na configuração adicionando ou removendo entradas na seção **investigações** do arquivo **Template. JSON** :

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
       Para obter mais informações sobre Azure Load Balancer investigações de integridade, consulte [investigações de integridade de Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Regras de NAT de entrada** -você pode adicionar ou remover regras de NAT de entrada para o balanceador de carga adicionando ou removendo entradas para a seção **inboundNatRules** do arquivo **Template. JSON** :

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
        Para concluir a adição ou remoção de uma regra NAT de entrada, a regra deve estar presente ou removida como uma propriedade de **tipo** no final do arquivo **Template. JSON** :

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
        Para obter mais informações sobre regras de NAT de entrada, consulte [o que é Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Regras de saída** – você pode adicionar ou remover regras de saída na configuração editando a propriedade **outboundRules** no arquivo **Template. JSON** :

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

         Para obter mais informações sobre as regras de saída, consulte [Load Balancer regras de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

12. Clique em **salvar** no editor online.
    
13. Clique em**assinatura** **básica** > para escolher a assinatura em que o balanceador de carga externo de destino será implantado.

15. Clique em**grupo de recursos** **básico** > para escolher o grupo de recursos em que o balanceador de carga de destino será implantado.  Você pode clicar em **criar novo** para criar um novo grupo de recursos para o balanceador de carga externo de destino ou escolher o grupo de recursos existente que foi criado acima para o IP público.  Verifique se o nome não é o mesmo que o grupo de recursos de origem do balanceador externo de carga de origem existente. 

16. Verifique se o**local** **básico** > está definido como o local de destino onde você deseja que o balanceador de carga externo seja implantado.

17. Verifique em **configurações** que o nome corresponde ao nome que você inseriu no editor de parâmetros acima.  Verifique se as IDs de recurso estão preenchidas para qualquer IPs público na configuração.

18. Marque a caixa em **termos e condições**.

19. Clique no botão **comprar** para implantar o IP público de destino.

## <a name="discard"></a>Dispensar 

Se você quiser descartar o IP público de destino e o balanceador de carga externo, exclua o grupo de recursos que contém o IP público de destino e o balanceador de carga externo.  Para fazer isso, selecione o grupo de recursos do seu painel no portal e selecione **excluir** na parte superior da página Visão geral.

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação do IP público e do balanceador de carga externo, exclua o IP público de origem e o balanceador de carga externo ou grupo de recursos. Para fazer isso, selecione o IP público e o balanceador de carga externo ou o grupo de recursos do seu painel no portal e selecione **excluir** na parte superior de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu um balanceador de carga externo do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
