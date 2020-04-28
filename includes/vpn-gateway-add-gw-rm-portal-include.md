---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78331303"
---
1. A partir do [menu do portal Azure,](https://portal.azure.com) selecione **Criar um recurso**. 

   ![Criar um recurso no portal Azure](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. No campo **Search the Marketplace,** digite 'Virtual Network Gateway'. Localize o gateway da **rede Virtual** na devolução de pesquisa e selecione a entrada. Na página de gateway da **rede Virtual,** selecione **Criar**. É aberta a página **Criar gateway de rede virtual**.
3. No separador **Basics,** preencha os valores para o seu portal de rede virtual.

   ![Criar campos de página de gateway de rede virtual](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Criar campos de página de gateway de rede virtual")

   ![Criar campos de página de gateway de rede virtual](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Criar campos de página de gateway de rede virtual")

   **Detalhes do projeto**

   - **Subscrição**: Selecione a subscrição que pretende utilizar a partir do dropdown.
   - **Grupo de Recursos**: Esta definição é preenchida automaticamente quando selecionar a sua rede virtual nesta página.

   **Detalhes da instância**

   - **Nome**: dê um nome ao gateway. Nomear o seu portal não é o mesmo que nomear uma subnet de gateway. É o nome do objeto de gateway que está a criar.
   - **Região**: Selecione a região em que pretende criar este recurso. A região para o portal deve ser a mesma que a rede virtual.
   - **Tipo de gateway**: selecione **VPN**. Os gateways de VPN utilizam o tipo de gateway de rede virtual **VPN**.
   - **Tipo de VPN**: selecione o tipo de VPN especificado para a sua configuração. A maioria das configurações requerem um tipo de VPN baseado em rotas.
   - **SKU**: selecione o SKU de gateway na lista pendente. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar. Para obter mais informações sobre os SKUs de gateway, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   - **Geração**: Para obter informações sobre vpN Gateway Generation, consulte [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   - **Rede virtual**: A partir da queda, selecione a rede virtual à qual pretende adicionar este portal.
   - Gama de **endereços de sub-rede gateway**: Este campo só aparece se o seu VNet não tiver uma sub-rede de gateway. Se possível, faça o intervalo /27 ou maior (/26,/25 etc.). Não recomendamos a criação de uma gama menor que /28. Se já tem uma subnet gateway, pode ver os detalhes da GatewaySubnet navegando para a sua rede virtual. Clique em **Subnets** para ver o alcance. Se quiser alterar o intervalo, pode eliminar e recriar a GatewaySubnet.

   **Endereço IP público**: esta definição especifica o objeto de endereço IP público que é associado ao gateway de VPN. O endereço IP público é dinamicamente atribuído a este objeto quando o gateway de VPN é criado. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

     - **Endereço IP público**: Deixe **Criar novo** selecionado.
     - **Nome do endereço IP público**: Na caixa de texto, escreva um nome para a sua instância de endereço IP público.
     - **Atribuição**: O gateway VPN suporta apenas a Dynamic.

   **Modo Active-Active**: Só **selecione** o modo ativa ativa ativa se estiver a criar uma configuração ativa de gateway. Caso contrário, deixe esta configuração não selecionada.

   Deixe **Configurar o BGP ASN** não selecionado, a não ser que a sua configuração solicite especificamente esta definição. Se precisar desta definição, o ASN predefinido é 65515, apesar de isto poder ser alterado.
4. Selecione **Review + criar** para executar validação. Assim que a validação passar, selecione **Criar** para implementar o gateway VPN. Um portal pode demorar até 45 minutos para criar e implantar totalmente. Pode ver o estado de implementação na página 'Overview' para o seu portal.

Uma vez criado o gateway, pode visualizar o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado.
