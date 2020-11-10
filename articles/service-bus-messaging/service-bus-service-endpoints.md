---
title: Configurar pontos finais de serviço de rede virtual para Azure Service Bus
description: Este artigo fornece informações sobre como adicionar um ponto final de serviço Microsoft.ServiceBus a uma rede virtual.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 8005a2c43d42908a9ad6ebea10b6a13ef381084c
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427654"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>Permitir o acesso ao espaço de nomes do Azure Service Bus a partir de redes virtuais específicas
A integração do Service Bus com [os pontos finais de serviço da Rede Virtual (VNet)][vnet-sep] permite o acesso seguro às capacidades de mensagens a partir de cargas de trabalho, como máquinas virtuais, que estão ligadas a redes virtuais, com o caminho de tráfego da rede a ser assegurado em ambas as extremidades.

Uma vez configurado para ser ligado a pelo menos um ponto final de serviço de sub-rede de rede virtual, o respetivo espaço de nome do Service Bus deixará de aceitar o tráfego de qualquer lugar, mas sim endereços IP de rede virtual autorizados e, opcionalmente, endereços IP específicos da Internet. Do ponto de vista da rede virtual, a ligação de um espaço de nomes de Service Bus a um ponto final de serviço configura um túnel de rede isolado da sub-rede de rede virtual para o serviço de mensagens.

O resultado é uma relação privada e isolada entre as cargas de trabalho ligadas à sub-rede e o respetivo espaço de nomes do Service Bus, apesar do endereço de rede observável do ponto final do serviço de mensagens estar numa gama pública de IP.

>[!WARNING]
> Implementar a integração de Redes Virtuais pode impedir que outros serviços da Azure interajam com o Service Bus. Como exceção, pode permitir o acesso aos recursos do Service Bus a partir de certos serviços fidedignos, mesmo quando os pontos finais do serviço de rede estão ativados. Para obter uma lista de serviços fidedignos, consulte [serviços Fidedignos.](#trusted-microsoft-services)
>
> Os seguintes serviços da Microsoft são obrigados a estar numa rede virtual
> - Serviço de Aplicações do Azure
> - Funções do Azure

> [!IMPORTANT]
> As Redes Virtuais são suportadas apenas em espaços de nomes de serviços de serviço de [nível Premium.](service-bus-premium-messaging.md) Ao utilizar pontos finais de serviço VNet com Service Bus, não deve ativar estes pontos finais em aplicações que misturem espaços de nomes standard e premium Tier Service Bus. Porque o nível padrão não suporta VNets. O ponto final é restrito apenas aos espaços de nome de nível Premium.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração do VNet 

As soluções que requerem uma segurança apertada e compartimentada, e onde as sub-redes de rede virtuais fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de vias de comunicação entre serviços residentes nesses compartimentos.

Qualquer via IP imediata entre os compartimentos, incluindo os que transportam HTTPS sobre TCP/IP, comporta o risco de exploração de vulnerabilidades a partir da camada de rede em cima. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são mesmo escritas para o disco à medida que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão ambas ligadas à mesma instância do Service Bus podem comunicar de forma eficiente e fiável através de mensagens, enquanto a respetiva integridade da fronteira de isolamento da rede é preservada.
 
Isto significa que as suas soluções de nuvem sensíveis à segurança não só têm acesso a capacidades de mensagens assíncronas fiáveis e escaláveis da indústria Azure, como podem agora utilizar mensagens para criar caminhos de comunicação entre compartimentos de solução seguros que são inerentemente mais seguros do que o que é possível com qualquer modo de comunicação peer-to-peer, incluindo HTTPS e outros protocolos de tomadas seguros.

## <a name="binding-service-bus-to-virtual-networks"></a>Autocarro de serviço de ligação para redes virtuais

*As regras de rede virtual* são a funcionalidade de segurança de firewall que controla se o seu servidor Azure Service Bus aceita ligações a partir de uma determinada sub-rede de rede virtual.

Ligar um espaço de nome de Service Bus a uma rede virtual é um processo em duas etapas. Primeiro, tem de criar um **ponto final de serviço de Rede Virtual** numa sub-rede de Rede Virtual e capacitá-lo para o **Microsoft.ServiceBus,** conforme explicado na [visão geral][vnet-sep]do ponto final do serviço . Depois de ter adicionado o ponto final do serviço, liga-lhe o espaço de nomes do Service Bus com uma **regra de rede virtual**.

A regra da rede virtual é uma associação do espaço de nomes do Service Bus com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho ligadas à sub-rede têm acesso ao espaço de nomes do Service Bus. A Service Bus nunca estabelece ligações de saída, não precisa de ter acesso e, portanto, nunca tem acesso à sua sub-rede, permitindo esta regra.

> [!NOTE]
> Lembre-se que um ponto final de serviço de rede fornece aplicações em execução na rede virtual o acesso ao espaço de nomes do Service Bus. A rede virtual controla a capacidade de acesso do ponto final, mas não que operações podem ser feitas nas entidades do Service Bus (filas, tópicos ou subscrições). Utilize o Azure Ative Directory (Azure AD) para autorizar operações que as aplicações possam realizar no espaço de nomes e suas entidades. Para mais informações, consulte [Authenticate e autorize uma aplicação com a Azure AD para aceder a entidades do Service Bus.](authenticate-application.md)


## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para adicionar um ponto final de serviço de rede virtual. Para limitar o acesso, é necessário integrar o ponto final do serviço de rede virtual para este espaço de nomes do Event Hubs.

1. Navegue para o seu espaço de nomes de **ônibus de serviço** no portal [Azure.](https://portal.azure.com)
2. No menu esquerdo, selecione a opção **de rede** em **Definições**.  

    > [!NOTE]
    > Você vê o **separador Networking** apenas para espaços de nome **premium.**  
    
    Por predefinição, a opção **de rede Selecionada** é selecionada. Se não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nesta página, o espaço de nome pode ser acedido através da internet pública (utilizando a chave de acesso).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Página de rede - padrão" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Se selecionar a opção **Todas as redes,** o seu espaço de nomes service bus aceita ligações a partir de qualquer endereço IP. Esta predefinição é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as opções de redes selecionadas](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. Para restringir o acesso a redes virtuais específicas, selecione a opção **redes Selecionadas** se ainda não estiver selecionada.
1. Na secção **Rede Virtual** da página, selecione **+Adicionar a rede virtual existente.** 

    ![adicionar rede virtual existente](./media/service-endpoints/add-vnet-menu.png)
3. Selecione a rede virtual a partir da lista de redes virtuais e, em seguida, escolha a **sub-rede**. Tem de ativar o ponto final de serviço antes de adicionar a rede virtual à lista. Se o ponto final de serviço não estiver ativado, o portal irá solicitar-lhe para o ativar.
   
   ![selecionar sub-redes](./media/service-endpoints/select-subnet.png)

4. Deverá ver a seguinte mensagem bem sucedida após o ponto final de serviço da sub-rede estar ativado para **o Microsoft.ServiceBus**. **Selecione Adicionar** na parte inferior da página para adicionar a rede. 

    ![selecionar sub-rede e ativar o ponto final](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se não conseguir ativar o ponto final do serviço, poderá ignorar o ponto final de serviço de rede virtual em falta utilizando o modelo de Gestor de Recursos. Esta funcionalidade não se encontra disponível no portal.
6. **Selecione Guarde** na barra de ferramentas para guardar as definições. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal. O botão **Guardar** deve ser desativado. 

    ![Salvar rede](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > Para obter instruções sobre como permitir o acesso a partir de endereços ou intervalos IP específicos, consulte [Permitir o acesso a partir de endereços ou intervalos IP específicos](service-bus-ip-filtering.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager
O modelo seguinte do Gestor de Recursos permite adicionar uma regra de rede virtual a um espaço de nomes de service bus existente.

Parâmetros do modelo:

* **nomespaceName** : Espaço de nome de ônibus de serviço.
* **virtualNetworkingSubnetId** : Caminho de gestor de recursos totalmente qualificado para a sub-rede de rede virtual; por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede predefinida de uma rede virtual.

> [!NOTE]
> Embora não existam regras de negação possíveis, o modelo de Gestor de Recursos Azure tem a ação padrão definida para **"Permitir"** que não restringe as ligações.
> Ao fazer as regras de Rede Virtual ou Firewalls, temos de alterar o **_"defaultAction"_**
> 
> De
> ```json
> "defaultAction": "Allow"
> ```
> para
> ```json
> "defaultAction": "Deny"
> ```
>

Modelo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implementar o modelo, siga as instruções para [O Gestor de Recursos Azure][lnk-deploy].

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre redes virtuais, consulte os seguintes links:

- [Pontos finais de serviço de rede virtual Azure][vnet-sep]
- [Filtragem IP do autocarro de serviço Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
