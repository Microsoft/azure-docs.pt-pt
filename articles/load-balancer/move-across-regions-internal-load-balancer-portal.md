---
title: Mover o Equilibrador interno de Carga Azure para outra região do Azure utilizando o portal Azure
description: Utilize o modelo do Gestor de Recursos Azure para mover o Azure internal Load Balancer de uma região Azure para outra usando o portal Azure
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698498"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Mover o Equilibrador de Carga Interno Azure para outra região utilizando o portal Azure

Há vários cenários em que gostaria de mover o seu equilibrador interno de carga de uma região para outra. Por exemplo, é melhor criar um equilibrador de carga interno com a mesma configuração para testes. Também pode querer mover um equilibrador interno de carga para outra região como parte do planeamento de recuperação de desastres.

Os equilibradores internos de carga não podem ser movidos de uma região para outra. No entanto, pode utilizar um modelo de Gestor de Recursos Azure para exportar a configuração existente e a rede virtual de um equilibrador de carga interno.  Em seguida, pode encenar o recurso noutra região exportando o equilibrador de carga e a rede virtual para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar os modelos para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [Quickstart: Criar e implementar modelos de Gestor de Recursos Azure utilizando o portal Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o equilibrador interno Azure se encontra na região de Azure, de onde pretende mover-se.

- Os equilibradores internos de carga não podem ser movidos entre regiões.  Terá de associar o novo equilibrador de carga aos recursos na região alvo.

- Para exportar uma configuração interna do balanceador de carga e implementar um modelo para criar um equilibrador de carga interno noutra região, você precisará do papel de Contribuinte de Rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que está a usar. Este layout inclui, mas não se limita a equilibrar cargas, grupos de segurança de rede, máquinas virtuais e redes virtuais.

- Verifique se a sua subscrição Azure permite criar equilibradores de carga internos na região-alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de equilibradores de carga para este processo.  Ver [limites de subscrição e serviços da Azure, quotas e constrangimentos](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
Os passos seguintes mostram como preparar o equilibrador de carga interno para o movimento utilizando um modelo de Gestor de Recursos e mover a configuração interna do balançador de carga para a região alvo utilizando o portal Azure.  Como parte deste processo, a configuração de rede virtual do balançador de carga interna deve ser incluída e deve ser feita primeiro antes de mover o balançador interno de carga.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Exportar o modelo de rede virtual e implementar a partir do portal Azure

1. Iniciar sessão no [portal Azure](https://portal.azure.com)  >  **Resource Groups**.
2. Localize o Grupo de Recursos que contém a rede virtual de origem e clique nela.
3. Selecione > **Definições**  >  **Modelo de exportação**.
4. Escolha **Implementar** na lâmina do **modelo de exportação.**
5. Clique **em modelo**  >  **Editar parâmetros** para abrir oparameters.js **no** ficheiro no editor online.
6. Para editar o parâmetro do nome de rede virtual, altere a propriedade de **valor** em **parâmetros:**

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Altere o valor do nome da rede virtual de origem no editor para um nome à sua escolha para o VNET alvo. Certifique-se de que encerra o nome em aspas.

8. Clique em **Guardar** no editor.

9. Clique no modelo de edição **do modelo** para abrir otemplate.js >  **Edit template** **no** ficheiro no editor online.

10. Para editar a região-alvo onde o VNET será movido, altere a propriedade de **localização** sob recursos:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

11. Para obter códigos de localização da região, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)  O código para uma região é o nome da região sem espaços, **central dos EUA central.**  =  **centralus**

12. Também pode alterar outros parâmetros no **template.jsno** ficheiro, se quiser, e é opcional dependendo dos seus requisitos:

    * **Address Space** - O espaço de endereço do VNET pode ser alterado antes de economizar modificando a secção de endereço **de**  >  **recursosSpace** e alterando a propriedade **de endereçoprefixos** no **template.jsno** ficheiro:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Sub-rede** - O nome da sub-rede e o espaço do endereço da sub-rede podem ser alterados ou adicionados modificando a secção de **sub-redes** do **template.jsno** ficheiro. O nome da sub-rede pode ser alterado alterando a propriedade **do nome.** O espaço de endereço da sub-rede pode ser alterado alterando a propriedade **addressPrefix** na **template.jsno** ficheiro:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         Na **template.jsem** ficheiro, para alterar o prefixo do endereço, deve ser editado em dois locais, a secção listada acima e a secção **de tipo** listada abaixo.  Alterar a **propriedade AddressPrefix** para corresponder à acima:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

13. Clique em **Guardar** no editor online.

14. Clique **em BASICS**  >  **Subscription** para escolher a subscrição onde o VNET alvo será implementado.

15. Clique **em BASICS**  >  **Resource group** para escolher o grupo de recursos onde o VNET alvo será implantado.  Pode clicar em **Criar novo** para criar um novo grupo de recursos para o VNET alvo.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do VNET existente.

16. Verifique **se a localização BASICS** está definida para o local alvo onde deseja que o  >  **Location** VNET seja implantado.

17. Verifique em **DEFINIÇÕES** que o nome corresponde ao nome que introduziu no editor de parâmetros acima.

18. Verifique a caixa em **termos e condições.**

19. Clique no botão **'Comprar'** para implementar a rede virtual alvo.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportar o modelo interno do balançador de carga e implementar a partir da Azure PowerShell

1. Iniciar sessão no [portal Azure](https://portal.azure.com)  >  **Resource Groups**.
2. Localize o Grupo de Recursos que contém o equilibrador de carga interno de origem e clique nele.
3. Selecione > **Definições**  >  **Modelo de exportação**.
4. Escolha **Implementar** na lâmina do **modelo de exportação.**
5. Clique **em modelo**  >  **Editar parâmetros** para abrir oparameters.js **no** ficheiro no editor online.

6. Para editar o parâmetro do nome do balançador de carga interno, altere o padrão de **propriedadeValue** do nome do equilibrador interno de carga de origem para o nome do equilibrador interno de carga do seu alvo, certifique-se de que o nome está nas ações:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Para editar o valor da rede virtual alvo que foi movida acima, você deve primeiro obter o ID de recurso e, em seguida, copiá-lo e colá-lo no **parameters.jsem** arquivo. Para obter o ID:

    1. Inicie sessão no [portal Azure](https://portal.azure.com)  >  **Resource Groups** em outro separador ou janela do navegador.
    2. Localize o grupo de recursos-alvo que contém a rede virtual movida a partir dos degraus acima e clique nela.
    3. Selecione > **Configurações**  >  **Propriedades**.
    4. Na lâmina à direita, realce o **ID do recurso** e copie-o para a área de transferência.  Em alternativa, pode clicar na cópia para o botão **de área de transferência** à direita do caminho do ID do **recurso.**
    5. Cole o ID do recurso na propriedade **padrão DoValue** no editor **editar parâmetros** abertos na outra janela ou separador do navegador:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Clique em **Guardar** no editor online.

7. Clique no modelo de edição **do modelo** para abrir otemplate.js >  **Edit template** **no** ficheiro no editor online.
8. Para editar a região-alvo onde será movida a configuração interna do balançador de carga, altere a propriedade de **localização** sob **recursos** na **template.jsem** arquivo:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Para obter códigos de localização da região, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)  O código para uma região é o nome da região sem espaços, **central dos EUA central.**  =  **centralus**

10. Também pode alterar outros parâmetros no modelo se escolher, e é opcional dependendo dos seus requisitos:

    * **Sku** - Pode alterar o sku do balançador de carga interno na configuração de padrão para básico ou básico para padrão, alterando a propriedade do nome **sku**  >  **name** no **template.jsno** ficheiro:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Para obter mais informações sobre as diferenças entre os balanceadores de carga sku básico e padrão, consulte [a visão geral do Balançor de Carga Padrão Azure](./load-balancer-overview.md)

    * **Regras de equilíbrio de carga** - Pode adicionar ou remover as regras de equilíbrio de carga na configuração adicionando ou removendo entradas na secção **loadBalancingRules** do **template.jsno** ficheiro:

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
       Para obter mais informações sobre regras de equilíbrio de carga, consulte [o que é o Equilibr de Carga Azure?](./load-balancer-overview.md)

    * **Sondas** - Pode adicionar ou remover uma sonda para o equilibrador de carga na configuração, adicionando ou removendo entradas na secção de **sondas** da **template.jsno** ficheiro:

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
       Para obter mais informações sobre as sondas de saúde do Azure Load Balancer, consulte [as sondas de saúde do Balancer de Carga](./load-balancer-custom-probe-overview.md)

    * **Regras NAT de entrada** - Pode adicionar ou remover as regras NAT de entrada para o equilibrador de carga adicionando ou removendo entradas para a secção **de inboundNatRules** do **template.jsem** ficheiro:

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
        Para completar a adição ou remoção de uma regra NAT de entrada, a regra deve ser presente ou removida como uma propriedade **tipo** no final do **template.jsem** arquivo:

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
        Para obter mais informações sobre as regras NAT de entrada, consulte [o que é o Equilibr de Carga Azure?](./load-balancer-overview.md)

12. Clique em **Guardar** no editor online.

13. Clique **em BASICS**  >  **Subscription** para escolher a subscrição onde o balanceador de carga interno alvo será implantado.

15. Clique no grupo **basics**  >  **para** escolher o grupo de recursos onde o balanceador de carga alvo será implantado.  Pode clicar em **Criar novo** para criar um novo grupo de recursos para o balanceador de carga interno alvo ou escolher o grupo de recursos existente que foi criado acima para a rede virtual.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do equilibrador interno de carga existente.

16. Verifique **o BASICS**  >  **A localização** está definida para o local alvo onde deseja que o balançador interno de carga seja implantado.

17. Verifique em **DEFINIÇÕES** que o nome corresponde ao nome que introduziu no editor de parâmetros acima.  Verifique se os IDs de recursos são preenchidos para quaisquer redes virtuais na configuração.

18. Verifique a caixa em **termos e condições.**

19. Clique no botão **'Comprar'** para implementar a rede virtual alvo.

## <a name="discard"></a>Eliminar

Se desejar descartar a rede virtual alvo e o balançador de carga interno, elimine o grupo de recursos que contém a rede virtual alvo e o balançador interno de carga.  Para tal, selecione o grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** no topo da página geral.

## <a name="clean-up"></a>Limpeza

Para comprometer as alterações e completar o movimento da rede virtual e do balançador de carga interno, elimine a rede virtual de origem e o equilibrador de carga interno ou grupo de recursos. Para tal, selecione a rede virtual e o equilibrador de carga interno ou grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** no topo de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu um equilibrador interno Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)