---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619740"
---
Pode criar uma VNet com o modelo de implementação do Resource Manager e o portal do Azure ao seguir estes passos. Para obter mais informações sobre redes virtuais, consulte a [visão geral da Rede Virtual.](../articles/virtual-network/virtual-networks-overview.md)

>[!NOTE]
>Para a VNet para ligar a uma localização no local, Coordene com o seu administrador de rede no local para extrair um intervalo de endereços IP que possa utilizar especificamente para esta rede virtual. Se existir um intervalo de endereços duplicados em ambos os lados da ligação VPN, o tráfego será encaminhado de modo inesperado. Além disso, se pretender ligar esta VNet a outra VNet, o espaço de endereços não pode sobrepor-se a outra VNet. Planear a configuração de rede em conformidade.
>
>

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Em **recursos de pesquisa, serviço e docs (G+/)** , tipo *rede virtual*.

   ![Localizar página de recursos da Rede Virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Localizar página de recursos de rede virtual")
1. Selecione **Rede Virtual** a partir dos resultados do **Marketplace.**

   ![Selecione rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Localizar página de recursos de rede virtual")
1. Na página **rede virtual,** clique em **Criar**.

   ![página de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Clique em Criar")
1. Assim que clicar em criar, a página de **rede virtual Create** abre.

   ![Criar página de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "Página Criar rede virtual")
1. Na página **Criar rede virtual**, configure as definições da VNet. Depois de preencher os campos, o ponto de exclamação vermelho se torna uma marca de verificação verde quando os caracteres que insira no campo são validados. Alguns valores estão autofilled, que pode ser substituído pelos seus próprios valores:

   - **Nome**: Introduza o nome da rede virtual.
   - **Espaço de endereços**: introduza o espaço de endereços. Se tiver vários espaços de endereços para adicionar, introduza o seu primeiro espaço de endereço aqui. Pode adicionar espaços de endereços adicionais mais tarde, depois de criar a VNet. Se a sua configuração necessitar de espaço de endereço IPv6, verifique a caixa de verificação para introduzir essa informação.
   - **Subscrição**: Verifique se a subscrição listada é a correta. Pode utilizar o menu pendente para mudar de subscrição.
   - **Grupo de recursos**: Selecione um grupo de recursos existente ou crie um novo, inserindo um nome para o seu novo grupo de recursos. Se estiver a criar um novo grupo, dê um nome de acordo com os valores de configuração planeados. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Localização**: selecione a localização da VNet. A localização determina onde os recursos que implementar nesta vnet vão viver.
   - **Sub-rede**: Adicione o **nome** da sub-rede e a gama de **endereços**de sub-rede . Pode adicionar sub-redes adicionais mais tarde, depois de criar a VNet.
   - **Proteção DDos**: Selecione **Basic,** a menos que pretenda utilizar o serviço Standard.
   - **Pontos finais**de serviço : Pode deixar esta definição como **Desativada,** a menos que a sua configuração especifique esta definição.
   - **Firewall**: Pode deixar esta definição como **Desativada,** a menos que a sua configuração especifique esta definição.
1. Clique em **Criar** para iniciar a implementação da rede virtual.
