---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b338f25e9771f5947fd494cfb00d0f6cb9ef67a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75450838"
---
1. Selecione **Ligar sites VPN** para abrir a página **connect sites.**

    ![ligar](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "ligar")

   Complete os seguintes campos:

   * Introduza uma chave pré-partilhada. Se não introduzir uma chave, azure autogera uma para si.
   * Selecione as definições protocole e IPsec. Consulte [detalhes IPSec padrão/personalizados] (detalhes do IPSec predefinido/personalizado] (detalhes do IPSec predefinido/personalizado] (detalhes do IPSechttps://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Selecione a opção adequada para **a Rota padrão de Propagação**. A opção **Enable** permite que o centro virtual propague uma rota padrão aprendida para esta ligação. Esta bandeira permite a propagação da rota padrão a uma ligação apenas se a rota padrão já for aprendida pelo centro Virtual WAN como resultado da implantação de uma firewall no centro, ou se outro local conectado tiver forçado o túnel ativado. A rota padrão não tem origem no centro virtual WAN.

2. Selecione **Ligar**.
3. Em poucos minutos, o site mostrará o estado de ligação e conectividade.

   ![estado](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Estado de ligação:** Este é o estado do recurso Azure para a ligação que liga o Site VPN ao gateway VPN do hub Azure. Uma vez que esta operação de plano de controlo seja bem sucedida, o gateway Azure VPN e o dispositivo VPN no local prosseguirão para estabelecer conectividade.

   **Estado da conectividade:** Este é o estado real de conectividade (data path) entre o gateway VPN do Azure no hub e o VpN Site. Pode mostrar qualquer um dos seguintes estados:

    * **Desconhecido**: Este estado é tipicamente visto se os sistemas de backend estão a trabalhar para a transição para outro estado.
    * **Ligação**: O portal Azure VPN está a tentar contactar o site VPN no local.
    * **Conectado**: A conectividade está estabelecida entre o gateway Azure VPN e o site VPN no local.
    * **Desligado**: Este estado é visto se, por qualquer motivo (no local ou em Azure), a ligação foi desligada.
4. Dentro de um site de VPN hub, pode ainda fazer o seguinte: 

   * Editar ou eliminar a Ligação VPN.
   * Elimine o site no portal Azure.
   * Descarregue uma configuração específica de ramificação para mais detalhes sobre o lado Azure usando o menu de contexto (...) ao lado do site. Se pretender descarregar a configuração de todos os sites conectados no seu hub, selecione **Baixar VPN Config** no menu superior.
