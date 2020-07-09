---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78301959"
---
Pode criar um VNet com o modelo de implementação do Gestor de Recursos e o portal Azure seguindo estes passos. Para obter mais informações sobre redes virtuais, consulte [a visão geral da Rede Virtual.](../articles/virtual-network/virtual-networks-overview.md)

>[!NOTE]
>Ao utilizar uma rede virtual como parte de uma arquitetura de premissas cruzadas, certifique-se de coordenar com o seu administrador de rede no local para esculpir uma gama de endereços IP que pode usar especificamente para esta rede virtual. Se existir uma gama de endereços duplicada em ambos os lados da ligação VPN, o tráfego será desviado de forma inesperada. Além disso, se pretender ligar esta rede virtual a outra rede virtual, o espaço de endereço não pode sobrepor-se à outra rede virtual. Planeie a configuração da sua rede em conformidade.
>
>

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Nos **recursos de pesquisa, serviço e docs (G+/)**, escreva *rede virtual.*

   ![Localizar página de recursos de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Localizar página de recursos de rede virtual")
1. Selecione **Rede Virtual** a partir dos resultados do **Marketplace.**

   ![Selecione rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Localizar página de recursos de rede virtual")
1. Na página **'Rede Virtual',** selecione **Criar**.

   ![página de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Selecione Criar")
1. Assim que selecionar **Criar,** abre-se a página **de rede virtual Create.**
1. No **separador Basics,** configurar **detalhes do Projeto** e detalhes de **exemplo** VNet.

   ![Separador básico](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Separador básico") Quando preenche os campos, vê uma marca de verificação verde quando os caracteres que entra no campo são validados. Alguns valores são preenchidos automaticamente, que pode substituir por seus próprios valores:

   - **Subscrição**: Verifique se a subscrição listada é a correta. Pode utilizar o menu pendente para mudar de subscrição.
   - **Grupo de recursos**: Selecione um grupo de recursos existente ou clique em **Criar novo** para criar um novo. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Nome**: Introduza o nome da sua rede virtual.
   - **Região**: Selecione a localização para o seu VNet. A localização determina onde viverão os recursos que implementa para este VNet.

1. No separador **endereços IP,** configure os valores. Os valores apresentados nos exemplos abaixo são para fins de demonstração. Ajuste estes valores de acordo com as definições que necessita.

   ![Separador de endereços IP](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Separador de endereços IP")  
   - **Espaço de endereço IPv4**: Por predefinição, um espaço de endereço é automaticamente criado. Pode clicar no espaço do endereço para o ajustar para refletir os seus próprios valores. Também pode adicionar espaços de endereços adicionais.
   - **IPv6**: Se a sua configuração necessitar de espaço de endereço IPv6, selecione a caixa **espacial de endereço Add IPv6** para introduzir essa informação.
   - **Sub-rede**: Se utilizar o espaço de endereço predefinido, uma sub-rede predefinida é criada automaticamente. Se alterar o espaço do endereço, tem de adicionar uma sub-rede. **Selecione + Adicione a sub-rede** para abrir a janela da **sub-rede Add.** Configure as seguintes definições e, em seguida, **selecione Adicionar** para adicionar os valores:
      - **Nome da sub-rede**: Neste exemplo, nomeamos a sub-rede "FrontEnd".
      - **Intervalo de endereços da sub-rede**: O intervalo de endereços para esta sub-rede.

1. No separador **Segurança,** neste momento, deixe os valores predefinidos:

   - **Proteção DDos**: Básico
   - **Firewall**: Desativado
1. Selecione **Review + criar** para validar as definições de rede virtual.
1. Depois de validadas as definições, **selecione Criar**.
