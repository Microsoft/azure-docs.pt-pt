---
title: Configurar o acesso para vários aplicativos lógicos ISEs-Azure
description: Para vários ambientes de serviço de integração (ISEs), você pode configurar um único endereço IP de saída público para acessar sistemas externos de aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f35b6836012df7ac3879070f7a85fbfb21b456c0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74549248"
---
# <a name="set-up-access-for-multiple-integration-service-environments-in-azure-logic-apps"></a>Configurar o acesso para vários ambientes de serviço de integração nos aplicativos lógicos do Azure

Ao trabalhar com os aplicativos lógicos do Azure, você pode configurar um [ISE ( *ambiente do serviço de integração* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para hospedar aplicativos lógicos que precisam de acesso a recursos em uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Se você tiver várias instâncias do ISE que precisam de acesso a outros pontos de extremidade que têm restrições de IP, implante um [Firewall do Azure](../firewall/overview.md) ou um [dispositivo de rede virtual](../virtual-network/virtual-networks-overview.md#filter-network-traffic) em sua rede virtual e roteie o tráfego de saída por meio desse firewall ou dispositivo de rede virtual. Você pode fazer com que todas as instâncias do ISE em sua rede virtual usem um endereço IP único, previsível e público para se comunicar com os sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais do firewall nos sistemas de destino para cada ISE. Este tópico mostra como rotear o tráfego de saída por meio de um firewall do Azure, mas você pode aplicar conceitos semelhantes a uma solução de virtualização de rede virtual, como um firewall de terceiros do Azure Marketplace.

## <a name="prerequisites"></a>Pré-requisitos

* Um firewall do Azure que é executado na mesma rede virtual que o ISE. Se você não tiver um firewall, primeiro [adicione uma sub-rede](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) denominada `AzureFirewallSubnet` à sua rede virtual. Em seguida, você pode [criar e implantar um firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) em sua rede virtual.

* Uma [tabela de rotas](../virtual-network/manage-route-table.md)do Azure. Se você não tiver uma, primeiro [crie uma tabela de rotas](../virtual-network/manage-route-table.md#create-a-route-table). Para obter mais informações sobre roteamento, consulte [Roteamento de tráfego de rede virtual](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Configurar tabela de rotas

1. Na [portal do Azure](https://portal.azure.com), selecione a tabela de rotas, por exemplo:

   ![Selecione a tabela de rotas com a regra para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Para [Adicionar uma nova rota](../virtual-network/manage-route-table.md#create-a-route), no menu tabela de rotas, selecione **rotas** > **Adicionar**.

   ![Adicionar rota para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. No painel **Adicionar rota** , [Configure a nova rota](../virtual-network/manage-route-table.md#create-a-route) com uma regra que especifica que todo o tráfego de saída para o sistema de destino segue esse comportamento:

   * Usa a [**solução de virtualização**](../virtual-network/virtual-networks-udr-overview.md#user-defined) como o tipo do próximo salto.

   * Vai para o endereço IP privado da instância de firewall como o endereço do próximo salto.

     Para localizar esse endereço IP, no menu do firewall, selecione **visão geral**, localize o endereço em **endereço IP privado**, por exemplo:

     ![Localizar endereço IP privado do firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Veja um exemplo que mostra como essa regra pode parecer:

   ![Configurar regra para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome da rota** | <*nome de rota exclusivo*> | Um nome exclusivo para a rota na tabela de rotas |
   | **Prefixo de endereço** | <*endereço de destino*> | O endereço do sistema de destino no qual você deseja que o tráfego vá. Certifique-se de usar a [notação CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para esse endereço. |
   | **Tipo de salto seguinte** | **Dispositivo virtual** | O [tipo de salto](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) usado pelo tráfego de saída |
   | **Endereço do próximo salto** | <*Firewall-Private-IP-address*> | O endereço IP privado para o firewall |
   |||

## <a name="set-up-network-rule"></a>Configurar regra de rede

1. No portal do Azure, localize e selecione o firewall. No menu firewall, em **configurações**, selecione **regras**. No painel regras, selecione **coleção de regras de rede** > **Adicionar coleção de regras de rede**.

   ![Adicionar coleção de regras de rede ao firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Na coleção, adicione uma regra que permita o tráfego para o sistema de destino.

   Por exemplo, suponha que você tenha um aplicativo lógico executado em um ISE e precise se comunicar com um sistema SFTP. Você cria uma coleção de regras de rede chamada `LogicApp_ISE_SFTP_Outbound`, que contém uma regra de rede chamada `ISE_SFTP_Outbound`. Essa regra permite o tráfego do endereço IP de qualquer sub-rede na qual o ISE é executado em sua rede virtual para o sistema de destino SFTP usando o endereço IP privado do firewall.

   ![Configurar regra de rede para o firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Propriedades da coleção de regras de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*Network-Rule-Collection-name*> | O nome da sua coleção de regras de rede |
   | **Prioridade** | > <*de nível de prioridade* | A ordem de prioridade a ser usada para executar a coleção de regras. Para obter mais informações, consulte [o que são alguns conceitos de firewall do Azure](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? |
   | **Ação** | **Permitir** | O tipo de ação a ser executado para esta regra |
   |||

   **Propriedades da regra de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*nome da regra de rede*> | O nome da regra de rede |
   | **Protocolo** | <*conexão-protocolos*> | Os protocolos de conexão a serem usados. Por exemplo, se você estiver usando regras NSG, selecione **TCP** e **UDP**, não apenas **TCP**. |
   | **Endereços de origem** | <*ISE-subnet-addresses*> | Os endereços IP de sub-rede onde o ISE é executado e onde o tráfego do seu aplicativo lógico é originado |
   | **Endereços de destino** | <*de destino-IP-address*> | O endereço IP do sistema de destino no qual você deseja que o tráfego fique |
   | **Portas de destino** | <*portas de destino*> | Todas as portas que o sistema de destino usa para comunicação de entrada |
   |||

   Para obter mais informações sobre regras de rede, consulte estes artigos:

   * [Configurar uma regra de rede](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Lógica de processamento da regra de firewall do Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Perguntas frequentes do firewall do Azure](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [CLI do Azure: AZ Network firewall Network-Rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Passos seguintes

* [Conectar-se a redes virtuais do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)