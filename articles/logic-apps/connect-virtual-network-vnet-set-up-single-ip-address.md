---
title: Criar um endereço IP de saída pública para ises
description: Saiba como criar um único endereço IP público de saída para ambientes de serviços de integração (ISEs) em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191518"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Criar um único endereço IP para um ou mais ambientes de serviço de integração em Aplicações Lógicas Azure

Quando trabalha com apps Azure Logic, pode configurar um ambiente de serviço de [ *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para hospedar aplicações lógicas que precisam de acesso a recursos numa [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) Quando tiver várias instâncias ISE que precisam de acesso a outros pontos finais que tenham restrições IP, coloque um [Firewall Azure](../firewall/overview.md) ou um [aparelho virtual](../virtual-network/virtual-networks-overview.md#filter-network-traffic) de rede na sua rede virtual e encaminhe o tráfego de saída através dessa firewall ou do aparelho virtual da rede. Em seguida, pode ter todos os casos ISE na sua rede virtual usando um único endereço IP, público, estático e previsível para comunicar com os sistemas de destino. Dessa forma, não é preciso configurar aberturas adicionais de firewall nesses sistemas de destino para cada ISE.

Este tópico mostra como direcionar o tráfego de saída através de uma Firewall Azure, mas pode aplicar conceitos semelhantes a um aparelho virtual de rede, como uma firewall de terceiros do Azure Marketplace. Embora este tópico se centre na configuração para várias instâncias ise, também pode usar esta abordagem para um único ISE quando o seu cenário requer limitar o número de endereços IP que precisam de acesso. Considere se os custos adicionais para a firewall ou o aparelho de rede virtual fazem sentido para o seu cenário. Saiba mais sobre [os preços da Firewall Azure.](https://azure.microsoft.com/pricing/details/azure-firewall/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma firewall Azure que funciona na mesma rede virtual que o seu ISE. Se não tiver uma firewall, adicione primeiro [uma sub-rede](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) que tenha o nome de `AzureFirewallSubnet` à sua rede virtual. Em seguida, pode [criar e implantar uma firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) na sua rede virtual.

* Uma mesa de [rota](../virtual-network/manage-route-table.md)Azure. Se não tiver uma, primeiro [crie uma mesa](../virtual-network/manage-route-table.md#create-a-route-table)de rota. Para obter mais informações sobre o encaminhamento, consulte o [encaminhamento](../virtual-network/virtual-networks-udr-overview.md)de tráfego da rede virtual .

## <a name="set-up-route-table"></a>Configurar tabela de rotas

1. No [portal Azure,](https://portal.azure.com)selecione a tabela de rotas, por exemplo:

   ![Selecione tabela de rota com regra para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Para [adicionar uma nova rota,](../virtual-network/manage-route-table.md#create-a-route)no menu de tabela de rotas, selecione **Rotas** > **Adicionar**.

   ![Adicione rota para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. No painel de **rota Adicionar,** [configurar a nova rota](../virtual-network/manage-route-table.md#create-a-route) com uma regra que especifica que todo o tráfego de saída para o sistema de destino segue este comportamento:

   * Utiliza o [**aparelho Virtual**](../virtual-network/virtual-networks-udr-overview.md#user-defined) como o próximo tipo de lúpulo.

   * Vai para o endereço IP privado para a instância de firewall como o próximo endereço de lúpulo.

     Para encontrar este endereço IP, no seu menu de firewall, selecione **Visão Geral,** encontre o endereço em **endereço IP privado,** por exemplo:

     ![Encontre o endereço IP privado firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Aqui está um exemplo que mostra como tal regra pode parecer:

   ![Definir regra para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome da rota** | < *> de nome único de rota* | Um nome único para a rota na tabela de rotas |
   | **Prefixo de endereço** | <> *de destino* | O endereço do sistema de destino para onde quer que o tráfego vá. Certifique-se de que utiliza a notação de [encaminhamento inter-domínio sem classe (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para este endereço. |
   | **Tipo de salto seguinte** | **Aparelho virtual** | O [tipo de lúpulo](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) que é usado pelo tráfego de saída |
   | **Próximo endereço de lúpulo** | <> de *endereços ip privados de firewall* | O endereço IP privado para a sua firewall |
   |||

## <a name="set-up-network-rule"></a>Configurar a regra da rede

1. No portal Azure, encontre e selecione a sua firewall. No menu firewall, em **Definições,** selecione **Regras**. No painel de regras, selecione **a recolha** de regras da rede > Adicionar a recolha de regras de **rede**.

   ![Adicione a recolha de regras de rede à firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Na coleção, adicione uma regra que permite o tráfego para o sistema de destino.

   Por exemplo, suponha que você tem uma aplicação lógica que funciona num ISE e precisa comunicar com um sistema SFTP. Cria-se uma coleção de regras de rede que se chama `LogicApp_ISE_SFTP_Outbound`, que contém uma regra de rede chamada `ISE_SFTP_Outbound`. Esta regra permite o tráfego a partir do endereço IP de qualquer subnet onde o seu ISE corre na sua rede virtual para o sistema SFTP de destino utilizando o endereço IP privado da sua firewall.

   ![Configurar a regra da rede para firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Propriedades de recolha de regras de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <> *de recolha de regras de rede* | O nome da sua coleção de regras de rede |
   | **Prioridade** | <> *de nível prioritário* | A ordem de prioridade a utilizar para executar a coleção de regras. Para mais informações, veja [quais são alguns conceitos de Firewall Azure?](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts) |
   | **Ação** | **Permitir** | O tipo de ação a executar para esta regra |
   |||

   **Propriedades de regras de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | < *> de nome da rede* | O nome da sua regra de rede |
   | **Protocolo** | <*protocolos de ligação*> | Os protocolos de ligação a utilizar. Por exemplo, se estiver a usar as regras do NSG, selecione **tCP** e **UDP**, não só **TCP**. |
   | **Endereços de origem** | <*endereços de sub-rede ISE*> | Os endereços IP da sub-rede onde o seu ISE corre e onde o tráfego da sua aplicação lógica é originário |
   | **Endereços de destino** | <> *de endereço de destino-IP* | O endereço IP para o seu sistema de destino onde pretende que o tráfego vá |
   | **Portos de destino** | < *> de destino-portos* | Quaisquer portas que o seu sistema de destino utilize para comunicação de entrada |
   |||

   Para obter mais informações sobre as regras da rede, consulte estes artigos:

   * [Configurar uma regra de rede](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Lógica de processamento de regras da Firewall Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure Firewall FAQ](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az network firewall network-rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se a redes virtuais Azure a partir de Aplicações Lógicas Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)