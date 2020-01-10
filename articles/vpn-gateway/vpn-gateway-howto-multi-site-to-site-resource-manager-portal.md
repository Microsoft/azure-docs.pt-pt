---
title: 'Adicionar várias conexões site a site de gateway de VPN a uma VNet: portal do Azure'
description: Adicionar conexões S2S multissite a um gateway de VPN que tem uma conexão existente
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 7b438f2b966dc43d41b91a138b39193d230d5546
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779693"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicionar uma conexão site a site a uma VNet com uma conexão de gateway de VPN existente

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
> 

Este artigo ajuda você a adicionar conexões site a site (S2S) a um gateway de VPN que tem uma conexão existente usando o portal do Azure. Esse tipo de conexão é geralmente chamado de configuração de "multissite". Você pode adicionar uma conexão S2S a uma VNet que já tem uma conexão S2S, conexão ponto a site ou conexão VNet a VNet. Há algumas limitações ao adicionar conexões. Marque a seção [antes de começar](#before) neste artigo para verificar antes de iniciar a configuração. 

Este artigo se aplica a VNets do Resource Manager que têm um gateway de VPN RouteBased. Essas etapas não se aplicam a novas configurações de conexão coexistentes do ExpressRoute/site a site. No entanto, se você estiver simplesmente adicionando uma nova conexão VPN a uma configuração Coexist existente, poderá usar estas etapas. Consulte [conexões coexistentes do ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obter informações sobre conexões coexistentes.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela conforme novos artigos e ferramentas adicionais ficam disponíveis para essa configuração. Quando um artigo está disponível, nós vinculamos diretamente a ele a partir desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Antes de começar
Verifique os seguintes itens:

* Você não está configurando uma nova configuração de gateway de ExpressRoute e VPN coexistente.
* Você tem uma rede virtual que foi criada usando o modelo de implantação do Gerenciador de recursos com uma conexão existente.
* O gateway de rede virtual para sua VNet é RouteBased. Se você tiver um gateway de VPN PolicyBased, deverá excluir o gateway de rede virtual e criar um novo gateway de VPN como RouteBased.
* Nenhum dos intervalos de endereços se sobrepõem a qualquer um dos VNets aos quais essa VNet está se conectando.
* Você tem um dispositivo VPN compatível e alguém que possa configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Você tem um endereço IP público voltado para o lado externo para seu dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.

## <a name="part1"></a>Parte 1-configurar uma conexão
1. Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **todos os recursos** e localize o **Gateway de rede virtual** na lista de recursos e clique nele.
3. Na página **Gateway de rede virtual** , clique em **conexões**.
   
    ![Página conexões](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Página conexões")<br>
4. Na página **conexões** , clique em **+ Adicionar**.
   
    ![Botão Adicionar conexão](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Botão Adicionar conexão")<br>
5. Na página **Adicionar conexão** , preencha os seguintes campos:
   
   * **Nome:** O nome que você deseja dar ao site ao qual você está criando a conexão.
   * **Tipo de conexão:** Selecione **site a site (IPSec)** .
     
     ![Página Adicionar conexão](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Página Adicionar conexão")<br>

## <a name="part2"></a>Parte 2-adicionar um gateway de rede local
1. Clique em **Gateway de rede local** ***escolha um gateway de rede local***. Isso abrirá a página **escolher gateway de rede local** .
   
    ![Escolher gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Escolher gateway de rede local")<br>
2. Clique em **criar novo** para abrir a página **criar gateway de rede local** .
   
    ![Página Criar gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Criar gateway de rede local")<br>
3. Na página **criar gateway de rede local** , preencha os seguintes campos:
   
   * **Nome:** O nome que você deseja dar ao recurso de gateway de rede local.
   * **Endereço IP:** O endereço IP público do dispositivo VPN no site ao qual você deseja se conectar.
   * **Espaço de endereço:** O espaço de endereço que você deseja rotear para o novo site de rede local.
4. Clique em **OK** na página **criar gateway de rede local** para salvar as alterações.

## <a name="part3"></a>Parte 3-adicionar a chave compartilhada e criar a conexão
1. Na página **Adicionar conexão** , adicione a chave compartilhada que você deseja usar para criar a conexão. Você pode obter a chave compartilhada do seu dispositivo VPN ou criar uma aqui e, em seguida, configurar seu dispositivo VPN para usar a mesma chave compartilhada. O importante é que as chaves são exatamente as mesmas.
   
    ![Chave compartilhada](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Chave partilhada")<br>
2. Na parte inferior da página, clique em **OK** para criar a conexão.

## <a name="part4"></a>Parte 4-verificar a conexão VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Consulte o [roteiro de aprendizagem de máquinas virtuais](/learn/paths/deploy-a-website-with-azure-virtual-machines/) para obter mais informações.
