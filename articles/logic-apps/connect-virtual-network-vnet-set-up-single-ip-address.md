---
title: Criar um endereço IP de saída pública para ises
description: Saiba como configurar um único endereço IP de saída pública para ambientes de serviços de integração (ISEs) em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e88c4bf05d88007a6e19b568f1bc1085e24b0325
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211061"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Crie um único endereço IP para um ou mais ambientes de serviço de integração em Azure Logic Apps

Quando trabalha com a Azure Logic Apps, pode criar um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para hospedar aplicações lógicas que precisam de acesso a recursos numa [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) Quando tiver várias instâncias ISE que precisam de acesso a outros pontos finais que tenham restrições IP, coloque um [Azure Firewall](../firewall/overview.md) ou um [aparelho virtual](../virtual-network/virtual-networks-overview.md#filter-network-traffic) de rede na sua rede virtual e encaminhe o tráfego de saída através dessa firewall ou aparelho virtual de rede. Em seguida, pode ter todas as instâncias ISE na sua rede virtual usar um único endereço IP público, estático e previsível para comunicar com os sistemas de destino que deseja. Desta forma, não precisa de configurar aberturas de firewall adicionais nos seus sistemas de destino para cada ISE.

Este tópico mostra como encaminhar o tráfego de saída através de um Azure Firewall, mas você pode aplicar conceitos semelhantes a um aparelho virtual de rede, como uma firewall de terceiros do Azure Marketplace. Embora este tópico se centre na configuração para várias instâncias ISE, também pode usar esta abordagem para um único ISE quando o seu cenário requer limitar o número de endereços IP que precisam de acesso. Considere se os custos adicionais para a firewall ou para o aparelho de rede virtual fazem sentido para o seu cenário. Saiba mais sobre [os preços do Azure Firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma firewall Azure que funciona na mesma rede virtual que o seu ISE. Se não tiver uma firewall, adicione primeiro [uma sub-rede](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) que tenha o nome da `AzureFirewallSubnet` sua rede virtual. Em seguida, pode [criar e implantar uma firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) na sua rede virtual.

* Uma tabela de [rotas Azure](../virtual-network/manage-route-table.md). Se não tiver um, primeiro [crie uma tabela de rotas.](../virtual-network/manage-route-table.md#create-a-route-table) Para obter mais informações sobre o encaminhamento, consulte [o encaminhamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Configurar tabela de rotas

1. No [portal Azure,](https://portal.azure.com)selecione a tabela de rotas, por exemplo:

   ![Selecione tabela de rotas com regra para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Para [adicionar uma nova rota](../virtual-network/manage-route-table.md#create-a-route), no menu de tabelas de rota, selecione **Rotas**  >  **Adicionar**.

   ![Adicionar rota para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. No painel de **rotas Add,** [crie a nova rota](../virtual-network/manage-route-table.md#create-a-route) com uma regra que especifica que todo o tráfego de saída para o sistema de destino segue este comportamento:

   * Utiliza o [**aparelho Virtual**](../virtual-network/virtual-networks-udr-overview.md#user-defined) como próximo tipo de lúpulo.

   * Vai para o endereço IP privado para a instância de firewall como o próximo endereço de lúpulo.

     Para encontrar este endereço IP, no seu menu de firewall, selecione **Overview**, encontre o endereço no **endereço IP privado**, por exemplo:

     ![Encontre o endereço IP privado de firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Aqui está um exemplo que mostra como tal regra pode parecer:

   ![Estabeleça regra para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome da rota** | <*unique route-name*> | Um nome único para o percurso na mesa de rotas |
   | **Prefixo de endereço** | <*endereço de destino*> | O prefixo de endereço para o seu sistema de destino para onde deseja que o tráfego de saída vá. Certifique-se de que utiliza a [notação de encaminhamento de Inter-Domain (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) sem classe para este endereço. Neste exemplo, este prefixo de endereço destina-se a um servidor SFTP, que é descrito na secção, [Configurar a regra da rede](#set-up-network-rule). |
   | **Tipo de salto seguinte** | **Aparelho virtual** | O [tipo de lúpulo](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) que é usado pelo tráfego de saída |
   | **Endereço do próximo salto** | <*firewall-private-IP-address*> | O endereço IP privado para a sua firewall |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Configurar regra de rede

1. No portal Azure, encontre e selecione a sua firewall. No menu de firewall, em **Definições**, selecione **Regras**. No painel de regras, selecione **Rede de recolha de regras** Adicionar a recolha de  >  **regras de rede**.

   ![Adicione a recolha de regras de rede à firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Na recolha, adicione uma regra que permite o tráfego para o sistema de destino.

   Por exemplo, suponha que tem uma aplicação lógica que funciona num ISE e precisa de comunicar com um servidor SFTP. Cria-se uma coleção de regras de rede que é denominada , que contém uma regra de `LogicApp_ISE_SFTP_Outbound` rede chamada `ISE_SFTP_Outbound` . Esta regra permite o tráfego a partir do endereço IP de qualquer sub-rede onde o seu ISE é executado na sua rede virtual para o servidor destino SFTP, utilizando o endereço IP privado da sua firewall.

   ![Configurar regra de rede para firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Propriedades de recolha de regras de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*rede-regra-recolha-nome*> | O nome da sua coleção de regras de rede |
   | **Priority** | <*nível prioritário*> | A ordem de prioridade a utilizar para executar a recolha de regras. Para mais informações, veja [quais são alguns conceitos de Azure Firewall?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts) |
   | **Ação** | **Permitir** | O tipo de ação a executar para esta regra |
   |||

   **Propriedades de regra de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*regra de rede-nome*> | O nome para a sua regra de rede |
   | **Protocolo** | <*protocolos de ligação*> | Os protocolos de ligação a utilizar. Por exemplo, se estiver a utilizar as regras NSG, selecione **tanto o TCP** como **o UDP**, não apenas **o TCP**. |
   | **Endereços de origem** | <*Endereços ise-subnet*> | Os endereços IP da sub-rede onde o seu ISE funciona e onde o tráfego da sua aplicação lógica é originário |
   | **Endereços de destino** | <*destino-endereço IP*> | O endereço IP para o seu sistema de destino para onde deseja que o tráfego de saída vá. Neste exemplo, este endereço IP é para o servidor SFTP. |
   | **Portas de destino** | <*portos de destino*> | Quaisquer portas que o seu sistema de destino utilize para comunicação de entrada |
   |||

   Para obter mais informações sobre as regras da rede, consulte estes artigos:

   * [Configurar uma regra de rede](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Lógica de processamento de regras do Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [FAQ do Azure Firewall](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: regra da rede de firewall da rede az](/cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se a redes virtuais Azure a partir de Apps Azure Logic](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
