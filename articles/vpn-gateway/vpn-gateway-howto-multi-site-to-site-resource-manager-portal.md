---
title: 'Adicione várias ligações Site a Site de gateway VPN a uma VNet: Portal do Azure: O Resource Manager | Documentos da Microsoft'
description: Adicionar as ligações de S2S de múltiplos sites a um gateway VPN que tenha uma ligação existente
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 4b9f007e00d0912687b723bd4f7e747da893948d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998827"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicionar uma ligação Site a Site a uma VNet com uma ligação de gateway VPN existente

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
> 

Este artigo ajuda-o a adicionar ligações Site a Site (S2S) a um gateway VPN que tenha uma ligação existente utilizando o portal do Azure. Este tipo de ligação é frequentemente referido como uma configuração de "multilocal". Pode adicionar uma ligação S2S a uma VNet que já tenha uma ligação S2S, a ligação ponto a Site ou a ligação de VNet a VNet. Existem algumas limitações ao adicionar ligações. Verifique os [antes de começar](#before) secção deste artigo para verificar antes de iniciar a configuração. 

Este artigo se aplica a VNets do Resource Manager que têm um gateway de RouteBased VPN. Estes passos não são aplicáveis a configurações de ligação coexistentes do ExpressRoute/Site para Site. Ver [ligações coexistentes do ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obter informações sobre ligações coexistentes.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela como novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando estiver disponível um artigo, vamos ligar diretamente a ele desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Antes de começar
Verifique se os seguintes itens:

* Não está a criar uma ligação de coexistentes do ExpressRoute/S2S.
* Tem uma rede virtual que foi criada com o modelo de implementação do Resource Manager com uma ligação existente.
* O gateway de rede virtual para a sua VNet é RouteBased. Se tiver um gateway de PolicyBased VPN, tem de eliminar o gateway de rede virtual e criar um novo gateway de VPN RouteBased.
* Nenhum dos intervalos de endereços se sobreponha para qualquer uma das VNets a que está a ligar a esta VNet.
* Tem o dispositivo VPN compatível e alguém que consiga configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Ter um endereço IP público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.

## <a name="part1"></a>Parte 1: configurar uma ligação
1. Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **todos os recursos** e localize seu **gateway de rede virtual** na lista de recursos e clique no mesmo.
3. Sobre o **gateway de rede Virtual** página, clique em **ligações**.
   
    ![Página de Ligações](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Página de Ligações")<br>
4. Sobre o **conexões** página, clique em **+ adicionar**.
   
    ![Adicionar botão de ligação](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "adicionar botão de ligação")<br>
5. Sobre o **adicionar ligação** página, preencha os campos seguintes:
   
   * **Nome:** O nome que pretende dar ao site que está a criar a ligação.
   * **Tipo de ligação:** Selecione **Site a site (IPsec)**.
     
     ![Adicionar página de ligação](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Adicionar página de ligação")<br>

## <a name="part2"></a>Parte 2: adicionar um gateway de rede local
1. Clique em **gateway de rede Local** ***escolher um gateway de rede local***. Esta ação abre o **gateway de rede local de escolha** página.
   
    ![Escolher gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "escolher gateway de rede local")<br>
2. Clique em **criar novo** para abrir o **criar o gateway de rede local** página.
   
    ![Página de criação de gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "criar gateway de rede local")<br>
3. Sobre o **criar gateway de rede local** página, preencha os campos seguintes:
   
   * **Nome:** O nome que pretende dar ao recurso de gateway de rede local.
   * **Endereço IP:** O endereço IP público do dispositivo VPN no site que deseja se conectar.
   * **Espaço de endereços:** O espaço de endereços que pretende ser encaminhado para o novo site de rede local.
4. Clique em **OK** sobre o **criar o gateway de rede local** página para guardar as alterações.

## <a name="part3"></a>Parte 3 - adicionar a chave partilhada e criar a ligação
1. Sobre o **adicionar ligação** página, adicione a chave partilhada que pretende utilizar para criar a ligação. Pode obter a chave partilhada do seu dispositivo VPN, ou criar uma aqui e, em seguida, configurar o dispositivo VPN para utilizar a mesma chave partilhada. O importante é que as chaves são exatamente os mesmos.
   
    ![Chave partilhada](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Chave partilhada")<br>
2. Na parte inferior da página, clique em **OK** para criar a ligação.

## <a name="part4"></a>Parte 4: verificar a ligação VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Consulte a [percurso de aprendizagem de máquinas de virtuais](/learn/paths/deploy-a-website-with-azure-virtual-machines/) para obter mais informações.
