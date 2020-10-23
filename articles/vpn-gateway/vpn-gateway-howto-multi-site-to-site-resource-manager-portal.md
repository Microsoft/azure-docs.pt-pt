---
title: 'Adicione várias ligações VPN gateway Site-to-Site a um VNet: Azure Portal'
description: Adicione ligações S2S multi-locais a um gateway VPN que tem uma ligação existente
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: ec2516010768eded939b0ffa44c197f102c7766b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "89401202"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicione uma ligação Site-a-Site a um VNet com uma ligação de gateway VPN existente

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
> 

Este artigo ajuda-o a adicionar ligações Site-to-Site (S2S) a um gateway VPN que tem uma ligação existente utilizando o portal Azure. Este tipo de ligação é frequentemente referido como uma configuração "multi-site". Pode adicionar uma ligação S2S a um VNet que já tem uma ligação S2S, ligação ponto-a-local ou ligação VNet-to-VNet. Existem algumas limitações na adição de ligações. Verifique a secção [Antes de começar](#before) a secção neste artigo para verificar antes de iniciar a sua configuração. 

Este artigo aplica-se aos VNets do Gestor de Recursos que têm uma porta VPN RouteBased. Estes passos não se aplicam às novas configurações de conexão coexistindo ExpressRoute/Site-to-Site. No entanto, se estiver apenas a adicionar uma nova ligação VPN a uma configuração coexistidora já existente, pode utilizar estes passos. Consulte [as ligações coexistindo ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obter informações sobre ligações coexistindo.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela à medida que novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo está disponível, ligamo-lo diretamente a partir desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar
Verifique os seguintes itens:

* Não está a configurar uma nova configuração expressRoute e VPN Gateway coexistindo.
* Tem uma rede virtual que foi criada utilizando o modelo de implementação do Gestor de Recursos com uma ligação existente.
* O portal de rede virtual para o seu VNet é RouteBased. Se tiver uma porta VPN VPN de 44 de política, deve eliminar o gateway de rede virtual e criar um novo gateway VPN como RouteBased.
* Nenhum dos intervalos de endereços se sobrepõe a nenhum dos VNets a que este VNet está a ligar.
* Tem um dispositivo VPN compatível e alguém que seja capaz de o configurar. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Tem um endereço IP público virado para o exterior para o seu dispositivo VPN.

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>Parte 1 - Configurar uma ligação
1. Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique **em Todos os recursos** e localize o seu portal de rede **virtual** a partir da lista de recursos e clique nele.
3. Na página **de gateway de rede Virtual,** clique em **Conexões**.
   
    ![Página de ligações](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Página de ligações")<br>
4. Na página **'Ligações',** clique **em +Adicionar**.
   
    ![Adicionar botão de conexão](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Adicionar botão de conexão")<br>
5. Na página **de ligação Adicionar,** preencha os seguintes campos:
   
   * **Nome:** O nome que pretende dar ao site a que está a criar a ligação.
   * **Tipo de ligação:** Selecione **Site-to-site (IPsec)**.
     
     ![Adicionar página de conexão](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Adicionar página de conexão")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>Parte 2 - Adicione uma porta de entrada de rede local
1. Clique **em Gateway de rede Local** Escolha um gateway de rede ** _local_*_. Isto abrirá a*** página _ Escolha a página de gateway de rede local.
   
    ![Escolha o gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Escolha o gateway de rede local")<br>
2. Clique **em Criar novo** para abrir a página de gateway de rede **local.**
   
    ![Criar página de gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Criar porta de rede local")<br>
3. Na página de gateway de **rede local,** preencha os seguintes campos:
   
   * **Nome:** O nome que pretende dar ao recurso de gateway de rede local.
   * **Endereço IP:** O endereço IP público do dispositivo VPN no site a que pretende ligar.
   * **Espaço de endereço:** O espaço de endereço que deseja ser encaminhado para o novo site da rede local.
4. Clique em **OK** na página **de gateway de rede local** para guardar as alterações.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>Parte 3 - Adicione a chave partilhada e crie a ligação
1. Na página **de ligação Adicionar,** adicione a chave partilhada que pretende utilizar para criar a sua ligação. Pode obter a chave partilhada do seu dispositivo VPN ou fazer uma aqui em cima e, em seguida, configurar o seu dispositivo VPN para usar a mesma chave partilhada. O importante é que as chaves são exatamente iguais.
   
    ![Chave partilhada](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Chave partilhada")<br>
2. Na parte inferior da página, clique **em OK** para criar a ligação.

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>Parte 4 - Verificar a ligação VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Consulte o [caminho de aprendizagem das máquinas virtuais](/learn/paths/deploy-a-website-with-azure-virtual-machines/) para obter mais informações.
