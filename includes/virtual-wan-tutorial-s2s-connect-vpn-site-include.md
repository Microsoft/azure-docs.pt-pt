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
ms.openlocfilehash: bcc4af837cc28a18345e49cd39ddf88d24e44dd8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488929"
---
1. Selecione **conectar sites VPN** para abrir a página **conectar sites** .

    ![Connect](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "ligar")

   Preencha os seguintes campos:

   * Insira uma chave pré-compartilhada. Se você não inserir uma chave, o Azure gerará uma para você.
   * Selecione as configurações de protocolo e IPsec. Consulte os detalhes de IPSec padrão/personalizado (Coloque o link para a página)
   * Selecione a opção apropriada para **propagar a rota padrão**. A opção **habilitar** permite que o Hub virtual propague uma rota padrão aprendida para essa conexão. Esse sinalizador habilita a propagação de rota padrão para uma conexão somente se a rota padrão já for aprendida pelo hub de WAN virtual como resultado da implantação de um firewall no Hub ou se outro site conectado tiver o túnel forçado habilitado. A rota padrão não é originada no Hub WAN virtual.

2. Selecione **Ligar**.
3. Em alguns minutos, o site mostrará o status de conexão e conectividade.

   ![Estado](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Status da conexão:** Esse é o status do recurso do Azure para a conexão que conecta o site VPN ao gateway de VPN do Hub do Azure. Quando essa operação do plano de controle for bem-sucedida, o gateway de VPN do Azure e o dispositivo VPN local continuarão a estabelecer a conectividade.

   **Status de conectividade:** Esse é o status real de conectividade (caminho de dados) entre o gateway de VPN do Azure no Hub e o site de VPN. Ele pode mostrar qualquer um dos seguintes Estados:

    * **Desconhecido**: esse Estado normalmente é visto se os sistemas de back-end estiverem trabalhando para fazer a transição para outro status.
    * **Conectando**: o gateway de VPN do Azure está tentando entrar em contato com o site VPN local real.
    * **Conectado**: a conectividade é estabelecida entre o gateway de VPN do Azure e o site de VPN local.
    * **Desconectado**: esse status é visto se, por qualquer motivo (local ou no Azure), a conexão foi desconectada.
4. Em um site de VPN de Hub, você também pode fazer o seguinte: 

   * Edite ou exclua a conexão VPN.
   * Exclua o site no portal do Azure.
   * Baixe uma configuração específica de ramificação para obter detalhes sobre o lado do Azure usando o menu de contexto (...) ao lado do site. Se você quiser baixar a configuração para todos os sites conectados em seu hub, selecione **baixar configuração de VPN** no menu superior.