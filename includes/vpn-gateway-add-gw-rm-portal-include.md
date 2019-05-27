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
ms.openlocfilehash: 72e61a36b58c0bc666f3e19b71fb1abe842208f5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150265"
---
1. Inicie sessão no portal do Azure e selecione **criar um recurso**. O **New** é aberta a página.

2. Na **pesquisar o campo de marketplace**, introduza *gateway de rede virtual*e selecione **gateway de rede Virtual** na lista de pesquisa. 

3. Sobre o **gateway de rede Virtual** página, selecione **criar** para abrir o **criar gateway de rede virtual** página.

   ![Create virtual network gateway page fields](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Create virtual network gateway page fields")

4. Sobre o **criar gateway de rede virtual** página, preencha os valores para o seu gateway de rede virtual:

   - **Nome**: Introduza um nome para o objeto de gateway que está a criar. Este nome é diferente do nome de sub-rede de gateway. 

   - **Tipo de gateway**: Selecione **VPN** para gateways de VPN. 

   - **Tipo de VPN**: Selecione o tipo VPN especificado para a sua configuração. A maioria das configurações requerem um **baseado na rota** tipo de VPN.

   - **SKU**: Selecione o SKU de gateway na lista pendente. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar. Para obter mais informações sobre os SKUs de gateway, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      Apenas a determinados **ative o modo ativo-ativo** se estiver a criar uma configuração de gateway ativo-ativo. Caso contrário, deixe esta configuração não selecionada.
  
   - **Localização**: Poderá ter de deslocar para ver **localização**. Definir **localização** para a localização onde se encontra a rede virtual. Por exemplo, **E.U.A. oeste**. Se não definir o local para a região onde está localizada a sua rede virtual, ele não aparece na lista pendente quando seleciona uma rede virtual.

   - **Rede virtual**: Escolha a rede virtual à qual pretende adicionar este gateway. Selecione **rede Virtual** para abrir o **escolher rede virtual** página e selecione a VNet. Se não vir a sua VNet, certifique-se de que o **localização** campo é definido para a região em que a sua rede virtual se encontra.

   - **Intervalo de endereços da sub-rede de gateway**: Só verá esta definição se não tiver criado anteriormente uma sub-rede de gateway da rede virtual. Se tiver criado anteriormente uma sub-rede de gateway válida, esta definição não será apresentado.

   - **Endereço IP público**: Esta definição especifica o objeto de endereço IP público associado o gateway de VPN. O endereço IP público é dinamicamente atribuído a este objeto quando o gateway de VPN é criado. O gateway VPN atualmente suporta apenas *dinâmica* alocação de endereço IP pública. No entanto, a alocação dinâmica não significa que o endereço IP é alterado depois de ser atribuído ao gateway de VPN. O único momento em que as alterações de endereço IP públicas é quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.
    
      - Deixe **Criar novo** selecionado.

      - Na caixa de texto, introduza um nome para o seu endereço IP público.

   - **Configurar BGP ASN**: Deixe esta definição desmarcada, a menos que a configuração requer especificamente-lo. Se necessitar desta definição, a predefinição ASN é *65515*, que pode alterar.
     
5. Verifique as definições e selecione **criar** para começar a criar o gateway de VPN. As definições são validadas e verá o **gateway de rede Virtual Implantando** mosaico no dashboard. A criação de um gateway pode demorar até 45 minutos. Poderá ter de atualizar a página do portal para ver o estado concluído.

6. Depois de criar o gateway, verifique se o endereço IP que é foram atribuído ao mesmo, visualizando a rede virtual no portal. O gateway aparece como um dispositivo ligado. Pode selecionar o dispositivo ligado (o seu gateway de rede virtual) para ver mais informações.