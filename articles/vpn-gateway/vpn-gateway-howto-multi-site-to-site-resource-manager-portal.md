---
title: 'Adicione várias ligações vpn gateway Site-to-Site a um VNet: Portal Azure'
description: Adicione ligações S2S multi-site a um gateway VPN que tem uma ligação existente
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 7b438f2b966dc43d41b91a138b39193d230d5546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779693"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicione uma ligação Site-a-Site a um VNet com uma ligação de gateway VPN existente

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
> 

Este artigo ajuda-o a adicionar ligações Site-to-Site (S2S) a um gateway VPN que tem uma ligação existente utilizando o portal Azure. Este tipo de ligação é frequentemente referido como uma configuração "multi-site". Pode adicionar uma ligação S2S a um VNet que já tem uma ligação S2S, ligação Ponto-a-Local ou ligação VNet-to-VNet. Existem algumas limitações ao adicionar ligações. Verifique a secção [Antes de começar](#before) neste artigo para verificar antes de iniciar a sua configuração. 

Este artigo aplica-se a VNets gestor de recursos que têm um gateway VPN baseado em rotas. Estes passos não se aplicam às novas configurações de ligação coexistente ExpressRoute/Site-to-Site. No entanto, se estiver apenas a adicionar uma nova ligação VPN a uma configuração coexistuante já existente, pode utilizar estes passos. Consulte [as ligações coexistentes ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obter informações sobre ligações coexistentes.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela à medida que novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo está disponível, ligamos diretamente a partir desta mesa.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar
Verifique os seguintes itens:

* Não está a configurar uma nova configuração de ExpressRoute coexistida e VPN Gateway.
* Tem uma rede virtual que foi criada utilizando o modelo de implementação do Gestor de Recursos com uma ligação existente.
* A porta de entrada de rede virtual para o seu VNet é RouteBased. Se tiver um gateway VPN baseado em políticas, deve eliminar o gateway da rede virtual e criar um novo gateway VPN como RouteBased.
* Nenhuma das gamas de endereços se sobrepõe a nenhum dos VNets a que este VNet está a ligar.
* Tem um dispositivo VPN compatível e alguém que é capaz de configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Tem um endereço IP público virado para o exterior para o seu dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>Parte 1 - Configurar uma ligação
1. Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **Todos os recursos** e localize o portal de rede **virtual** a partir da lista de recursos e clique nele.
3. Na página de gateway da **rede Virtual,** clique em **Ligações**.
   
    ![Página de ligações](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Página de ligações")<br>
4. Na página **Ligações,** clique em **+Adicionar**.
   
    ![Adicionar botão de ligação](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Adicionar botão de ligação")<br>
5. Na página de **ligação Adicionar,** preencha os seguintes campos:
   
   * **Nome:** O nome que pretende dar ao site a que está a criar a ligação.
   * **Tipo de ligação:** Selecione **Site-a-site (IPsec)**.
     
     ![Adicionar página de ligação](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Adicionar página de ligação")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>Parte 2 - Adicione um portal de rede local
1. Clique em **gateway de rede local** Escolha um gateway de rede ***local***. Isto abrirá a página de gateway da **rede local Escolha.**
   
    ![Escolha o gateway da rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Escolha o gateway da rede local")<br>
2. Clique **em Criar novo** para abrir a página de gateway da rede local **Criar.**
   
    ![Criar página de gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Criar gateway de rede local")<br>
3. Na página de gateway da **rede Create local,** preencha os seguintes campos:
   
   * **Nome:** O nome que quer dar ao recurso de gateway da rede local.
   * **Endereço IP:** O endereço IP público do dispositivo VPN no site a que pretende ligar.
   * **Espaço de endereço:** O espaço de endereço que pretende ser encaminhado para o novo site da rede local.
4. Clique em **OK** na página de gateway da **rede local Criar** para guardar as alterações.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>Parte 3 - Adicione a chave partilhada e crie a ligação
1. Na página de **ligação Adicionar,** adicione a chave partilhada que pretende utilizar para criar a sua ligação. Pode obter a chave partilhada do seu dispositivo VPN, ou fazer uma aqui e, em seguida, configurar o seu dispositivo VPN para usar a mesma chave partilhada. O importante é que as chaves são exatamente as mesmas.
   
    ![Chave partilhada](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Chave partilhada")<br>
2. Na parte inferior da página, clique em **OK** para criar a ligação.

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>Parte 4 - Verificar a ligação VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Consulte o caminho de aprendizagem das [máquinas virtuais](/learn/paths/deploy-a-website-with-azure-virtual-machines/) para obter mais informações.
