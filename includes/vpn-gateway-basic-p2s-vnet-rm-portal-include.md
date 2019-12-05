---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a826bb01a906153b4ca2a3c854623c83b6192aec
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828834"
---
Para criar uma VNet no modelo de implementação do Gestor de Recursos com o Portal do Azure, siga os passos abaixo. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus. Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição Geral da Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Se quiser que esta VNet ligue a uma localização no local (além de criar uma configuração P2S), terá de se coordenar com o administrador da rede no local para extrair um intervalo de endereços IP que possa utilizar especificamente para esta rede virtual. Se existir um intervalo de endereços duplicados em ambos os lados da ligação VPN, o tráfego não será encaminhado da forma esperada. Além disso, se pretender ligar esta VNet a outra VNet, o espaço de endereços não pode sobrepor-se a outra VNet. Tenha o cuidado de planear a configuração da rede em conformidade.
>
>

1. Inicie sessão no [portal do Azure](https://portal.azure.com).  No menu portal do Azure ou na **Home** Page do e selecione **criar um recurso**. O **New** é aberta a página.

2. Em **Pesquisar no Marketplace**, insira *rede virtual* e selecione **rede virtual** nos resultados.

   ![Página localizar recurso de rede virtual](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Página localizar recurso de rede virtual")

3. Perto da parte inferior do painel Virtual Network, na página **Selecionar um modelo de implementação**, selecione **Resource Manager** e clique em **Criar**.

   ![Selecionar Gerenciador de recursos](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "Selecionar o Resource Manager")
4. Na página **Criar rede virtual**, configure as definições da VNet. Depois de preencher os campos, o sinal de exclamação vermelho muda para uma marca de verificação verde se os caracteres introduzidos nos campos forem válidos. Poderá haver valores preenchidos automaticamente. Se for esse o caso, substitua-os pelos seus próprios valores. A página **Criar rede virtual** é semelhante ao exemplo seguinte:

   ![Validação de campo](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "Validação de campos")
5. **Nome**: introduza o nome da Rede Virtual.
6. **Espaço de endereços**: introduza o espaço de endereços. Se tiver vários espaços de endereços para adicionar, adicione o primeiro. Pode adicionar os outros mais tarde, depois de criar a VNet.
7. **Subscrição**: verifique se a Subscrição listada é a correta. Pode utilizar o menu pendente para mudar de subscrição.
8. **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo ao escrever o nome do mesmo. Se estiver a criar um novo grupo, dê-lhe um nome de acordo com os valores de configuração planeados. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
9. **Localização**: selecione a localização da VNet. A localização determina onde é que os recursos que implementar nesta VNet vão residir.
10. **Sub-rede**: adicione o nome e o intervalo de endereços da sub-rede. Pode adicionais outras sub-redes mais tarde, depois de criar a VNet.
11. Selecione **Afixar ao dashboard** se pretender conseguir localizar mais facilmente as suas VNet no dashboard e clique em **Criar**.

    ![Afixar ao dashboard](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "fixar no painel")
12. Depois de clicar em **Criar**, verá um mosaico no dashboard que irá refletir o progresso da VNet. O mosaico muda enquanto a VNet está a ser criada.

    ![Bloco criando rede virtual](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "Mosaico Criar rede virtual")
