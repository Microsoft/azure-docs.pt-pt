---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171605"
---
Pode criar uma VNet com o modelo de implementação do Resource Manager e o portal do Azure ao seguir estes passos. Para obter mais informações sobre as redes virtuais, consulte [descrição geral da rede Virtual](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Para a VNet para ligar a uma localização no local, Coordene com o seu administrador de rede no local para extrair um intervalo de endereços IP que possa utilizar especificamente para esta rede virtual. Se existir um intervalo de endereços duplicados em ambos os lados da ligação VPN, o tráfego será encaminhado de modo inesperado. Além disso, se pretender ligar esta VNet a outra VNet, o espaço de endereços não pode sobrepor-se a outra VNet. Planear a configuração de rede em conformidade.
>
>

1. Inicie sessão para o [portal do Azure](http://portal.azure.com) e selecione **criar um recurso**. O **New** é aberta a página.

2. Na **pesquisar no marketplace** , insira *rede virtual* e selecione **rede Virtual** da lista devolvida. O **rede Virtual** é aberta a página.

   ![Localizar página de recursos da Rede Virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Localizar página de recursos da Rede Virtual")

3. Do **selecionar um modelo de implementação** lista junto à parte inferior da página, selecione **Gestor de recursos**e, em seguida, selecione **criar**. O **criar rede virtual** é aberta a página.

   ![Página Criar rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Página Criar rede virtual")

4. Na página **Criar rede virtual**, configure as definições da VNet. Depois de preencher os campos, o ponto de exclamação vermelho se torna uma marca de verificação verde quando os caracteres que insira no campo são validados. Alguns valores estão autofilled, que pode ser substituído pelos seus próprios valores:

   - **Nome**: Introduza o nome da rede virtual.

   - **Espaço de endereços**: Introduza o espaço de endereços. Se tiver vários espaços de endereços para adicionar, introduza o seu primeiro espaço de endereço aqui. Pode adicionar espaços de endereços adicionais mais tarde, depois de criar a VNet.

   - **Subscrição**: Certifique-se de que a subscrição listada é a correta. Pode utilizar o menu pendente para mudar de subscrição.

   - **Grupo de recursos**: Selecione um grupo de recursos existente ou crie um novo ao introduzir um nome para o novo grupo de recursos. Se estiver a criar um novo grupo, dê um nome de acordo com os valores de configuração planeados. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Localização**: Selecione a localização para a sua VNet. A localização determina onde os recursos que implementar nesta vnet vão viver.

   - **Sub-rede**: Adicione a sub-rede **Name** e a sub-rede **intervalo de endereços**. Pode adicionar sub-redes adicionais mais tarde, depois de criar a VNet. 
     
5. Selecione **Criar**.
