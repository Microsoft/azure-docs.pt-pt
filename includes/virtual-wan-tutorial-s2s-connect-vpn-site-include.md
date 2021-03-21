---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 614a13a140453e3c7ed55a7fc0f9173626ad2f2f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627721"
---
1. Selecione **Connect VPN Sites** para abrir a página **de sites Connect.**

    ![O screenshot mostra o painel de Sites Conectados para O HUB Virtual pronto para uma chave pré-partilhada e definições associadas.](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "ligar")

   Complete os seguintes campos:

   * Introduza uma chave pré-partilhada. Se não introduzires uma chave, o Azure autogera-te uma.
   * Selecione as definições de Protocolo e IPsec. Para obter mais informações, consulte [o IPsec predefinido/personalizado](../articles/virtual-wan/virtual-wan-ipsec.md).
   * Selecione a opção adequada para **Propagar a Rota Padrão**. A opção **Enable** permite que o hub virtual propague uma rota padrão aprendida para esta ligação. Esta bandeira só permite a propagação por padrão da rota para uma ligação se a rota padrão já for aprendida pelo hub Virtual WAN como resultado da implantação de uma firewall no hub, ou se outro site conectado tiver feito um túnel forçado. A rota padrão não tem origem no hub Virtual WAN.

2. Selecione **Ligar**.
3. Após alguns minutos, o site mostrará o estado de ligação e conectividade.

   ![A screenshot mostra um site para a ligação do local e estado de conectividade.](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Estado de ligação:** Este é o estado do recurso Azure para a ligação que liga o site VPN ao gateway VPN do hub Azure. Uma vez que esta operação do avião de controlo seja bem sucedida, o gateway Azure VPN e o dispositivo VPN no local procederão para estabelecer conectividade.

   **Estado da conectividade:** Este é o estado real de conectividade (caminho de dados) entre a porta VPN da Azure no centro e o site VPN. Pode mostrar qualquer um dos seguintes estados:

    * **Desconhecido**: Este estado é tipicamente visto se os sistemas de backend estão a trabalhar para a transição para outro estado.
    * **Ligação**: O gateway Azure VPN está a tentar chegar ao local real de VPN.
    * **Conectado**: A conectividade é estabelecida entre o gateway Azure VPN e o local VPN no local.
    * **Desligado**: Este estado é visto se, por qualquer motivo (no local ou em Azure), a ligação foi desligada.
4. Dentro de um site hub VPN, você pode ainda fazer o seguinte: 

   * Editar ou eliminar a Ligação VPN.
   * Elimine o site no portal Azure.
   * Faça o download de uma configuração específica do ramo para mais detalhes sobre o lado Azure usando o menu contexto (...) ao lado do site. Se pretender descarregar a configuração para todos os sites conectados no seu hub, selecione **Download VPN Config** no menu superior.
