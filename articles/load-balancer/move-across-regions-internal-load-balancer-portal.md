---
title: Mova o Balancer interno azure para outra região do Azure usando o portal Azure
description: Use o modelo do Gestor de Recursos Azure para mover o Equilíbrio de Carga Interna Azure de uma região do Azure para outra usando o portal Azure
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75638814"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Mova o Balancer interno azure para outra região usando o portal Azure

Há vários cenários em que você gostaria de mover o seu equilibrador interno de carga existente de uma região para outra. Por exemplo, pode querer criar um equilibrador de carga interno com a mesma configuração para o teste. Também pode querer mover um equilibrador interno de carga para outra região como parte do planeamento de recuperação de desastres.

Os equilibradores de carga internos azure não podem ser movidos de uma região para outra. No entanto, pode utilizar um modelo do Gestor de Recursos Azure para exportar a configuração existente e a rede virtual de um equilibrador de carga interna.  Em seguida, pode encenar o recurso noutra região exportando o equilibrante de carga e a rede virtual para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar os modelos para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [Quickstart: Crie e implemente modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)do Gestor de Recursos Azure utilizando o portal Azure .


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o equilibrador de carga interna Azure está na região de Azure, de onde pretende mover-se.

- Os equilibradores de carga internos azure não podem ser movidos entre regiões.  Terá de associar o novo equilibrador de carga aos recursos na região alvo.

- Para exportar uma configuração interna do equilíbrio de carga e implementar um modelo para criar um equilibrador de carga interno noutra região, você precisará do papel de Colaborador de Rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede, máquinas virtuais e redes virtuais.

- Verifique se a sua subscrição Azure permite criar equilibradores de carga internos na região alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de equilibradores de carga para este processo.  Ver [limites de subscrição e serviço do Azure, quotas e constrangimentos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
Os seguintes passos mostram como preparar o equilibrador de carga interna para o movimento utilizando um modelo de Gestor de Recursos, e mover a configuração interna do equilíbrio de carga para a região alvo utilizando o portal Azure.  Como parte deste processo, a configuração da rede virtual do equilibrista interno de carga deve ser incluída e deve ser feita primeiro antes de mover o equilibrador interno de carga.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Exportar o modelo de rede virtual e implantar a partir do portal Azure

1. Inicie sessão nos > **grupos**de recursos do [portal Azure.](https://portal.azure.com)
2. Localize o Grupo de Recursos que contém a rede virtual de origem e clique nela.
3. Selecione > Modelo**de exportação**de **definições** > .
4. Escolha **implementar** na lâmina do **modelo de exportação.**
5. Clique em **MODELO** > **Editar parâmetros** para abrir o ficheiro **parâmetros.json** no editor online.
6. Para editar o parâmetro do nome da rede virtual, altere a propriedade de **valor** em **parâmetros:**

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
7. Altere o valor de nome de rede virtual de origem no editor para um nome à sua escolha para o VNET alvo. Certifique-se de que encerra o nome em aspas.

8. Clique em **Guardar** no editor.

9. Clique no**modelo de edição** de **MODELO** > para abrir o ficheiro **template.json** no editor online.

10. Para editar a região alvo onde o VNET será movido, altere a propriedade de **localização** sob recursos:

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

11. Para obter códigos de localização da região, consulte [localizações azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços,**central** **dos EUA** = central.

12. Também pode alterar outros parâmetros no ficheiro **template.json** se escolher, e é opcional dependendo dos seus requisitos:

    * **Endereço Espaço** - O espaço de endereço do VNET pode ser alterado antes de poupar modificando a secção de **endereços de recursosSpace** > **addressSpace** e alterando a propriedade **de endereçoPrefixos** no ficheiro **template.json:**

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

    * **Subnet** - O nome da sub-rede e o espaço de endereço da sub-rede podem ser alterados ou adicionados modificando a secção de **sub-redes** do ficheiro **template.json.** O nome da subnet pode ser alterado alterando a propriedade do **nome.** O espaço de endereço de sub-rede pode ser alterado alterando a propriedade **de endereçoPrefix** no ficheiro **template.json:**

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

         No ficheiro **template.json,** para alterar o prefixo de endereço, deve ser editado em dois lugares, a secção listada acima e a secção de **tipo** listada abaixo.  Altere a propriedade **de endereçoPrefix** para corresponder à acima:

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

14. Clique em**Subscrição** **BASICS** > para escolher a subscrição onde o Alvo VNET será implementado.

15. Clique no grupo **BASICS** > **Resource** para escolher o grupo de recursos onde o VNET alvo será implantado.  Pode clicar **em Criar um novo** grupo de recursos para o VNET alvo.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do VNET existente.

16. Verifique se a**localização** **BASICS** > está definida para o local alvo onde deseja que o VNET seja implantado.

17. Verifique em **DEFINIÇÕES** que o nome corresponde ao nome que inseriu no editor de parâmetros acima.

18. Verifique a caixa em **termos e condições.**

19. Clique no botão **Comprar** para implementar a rede virtual alvo.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de equilíbrio interno de carga e implantar a partir de Azure PowerShell

1. Inicie sessão nos > **grupos**de recursos do [portal Azure.](https://portal.azure.com)
2. Localize o Grupo de Recursos que contém o equilibrador de carga interno fonte e clique nele.
3. Selecione > Modelo**de exportação**de **definições** > .
4. Escolha **implementar** na lâmina do **modelo de exportação.**
5. Clique em **MODELO** > **Editar parâmetros** para abrir o ficheiro **parâmetros.json** no editor online.

6. Para editar o parâmetro do nome do equilibrista interno de carga, altere o padrão de **propriedadeValor** do nome do equilibrista de carga interna fonte para o nome do seu equilibrador de carga interna alvo, certifique-se de que o nome está em aspas:

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

6. Para editar o valor da rede virtual alvo que foi movida acima, você deve primeiro obter o ID de recurso e, em seguida, copiá-lo e colar no ficheiro **parâmetros.json.** Para obter a identificação:

    1. Inicie sessão no [portal](https://portal.azure.com) > Azure Resource**Groups** em outro separador ou janela do navegador.
    2. Localize o grupo de recursos alvo que contém a rede virtual deslocada a partir dos degraus acima e clique nele.
    3. Selecione >**Propriedades de** **Definições** > .
    4. Na lâmina à direita, realce o ID do **recurso** e copie-o para a área de reparação.  Em alternativa, pode clicar na **cópia para botão de clipboard** à direita do caminho de ID de **recurso.**
    5. Colar o ID de recurso na propriedade **defaultValue** no editor **de Edição Parâmetros** aberto na outra janela ou separador do navegador:

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

7. Clique no**modelo de edição** de **MODELO** > para abrir o ficheiro **template.json** no editor online.
8. Para editar a região alvo onde será movida a configuração interna do equilíbrio dormente, altere a propriedade de **localização** sob **recursos** no ficheiro **template.json:**

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

9.  Para obter códigos de localização da região, consulte [localizações azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços,**central** **dos EUA** = central.

10. Também pode alterar outros parâmetros do modelo se escolher, e é opcional dependendo dos seus requisitos:

    * **Sku** - Pode alterar o sku do equilibrador de carga interna na configuração de padrão para básico ou básico para padrão alterando a propriedade de**nome** **sku** > no ficheiro **template.json:**

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
      Para obter mais informações sobre as diferenças entre os equilibradores básicos e standard de carga sku, consulte a visão geral do [Equilíbrio de Carga Padrão Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Regras** de equilíbrio de carga - Pode adicionar ou remover regras de equilíbrio de carga na configuração adicionando ou removendo entradas na secção **loadBalancingRules** do ficheiro **template.json:**

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
       Para obter mais informações sobre as regras de equilíbrio de carga, consulte [o que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondas** - Pode adicionar ou remover uma sonda para o equilibrador de carga na configuração adicionando ou removendo entradas na secção de **sondas** do ficheiro **template.json:**

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
       Para obter mais informações sobre as sondas de saúde Azure Load Balancer, consulte sondas de [saúde Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Regras nat de entrada** - Pode adicionar ou remover as regras nat de entrada para o equilibrador de carga adicionando ou removendo entradas na secção **inboundNatRules** do ficheiro **template.json:**

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
        Para completar a adição ou remoção de uma regra NAT de entrada, a regra deve ser presente ou removida como uma propriedade **tipo** no final do ficheiro **template.json:**

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
        Para obter mais informações sobre as regras de entrada na NAT, consulte [o que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

12. Clique em **Guardar** no editor online.

13. Clique em**Subscrição** **BASICS** > para escolher a subscrição onde será implementado o equilibrador de carga interna alvo.

15. Clique no grupo **BASICS** > **Resource** para escolher o grupo de recursos onde o equilibrador de carga-alvo será implantado.  Pode clicar **em Criar um novo** grupo de recursos para o equilibrador de carga interna alvo ou escolher o grupo de recursos existente que foi criado acima para a rede virtual.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do equilibrador de carga interna de origem existente.

16. Verifique se a**localização** **BASICS** > está definida para o local-alvo onde deseja que o equilibrista interno de carga seja implantado.

17. Verifique em **DEFINIÇÕES** que o nome corresponde ao nome que inseriu no editor de parâmetros acima.  Verifique se os IDs de recursos são povoados para quaisquer redes virtuais na configuração.

18. Verifique a caixa em **termos e condições.**

19. Clique no botão **Comprar** para implementar a rede virtual alvo.

## <a name="discard"></a>Eliminar

Se pretender descartar a rede virtual alvo e o equilibrador de carga interna, elimine o grupo de recursos que contém a rede virtual alvo e o equilibrador de carga interna.  Para isso, selecione o grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** na parte superior da página de visão geral.

## <a name="clean-up"></a>Limpeza

Para comprometer as alterações e completar o movimento da rede virtual e do equilibrador de carga interna, elimine a rede virtual de origem e o equilíbrio de carga interna ou o grupo de recursos. Para isso, selecione a rede virtual e o balanceador de carga interna ou o grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** no topo de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um equilibrador de carga interna Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
