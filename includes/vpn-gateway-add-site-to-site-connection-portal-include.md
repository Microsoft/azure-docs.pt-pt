---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "92479589"
---
1. Abra a página para o seu portal de rede virtual. Pode navegar até ao portal indo para o **nome do seu VNet -> overview -> Dispositivos conectados -> Nome do seu gateway** , embora existam várias outras formas de navegar também.
1. Na página para o portal, selecione **Connections** . Na parte superior da página 'Ligações', **selecione +Adicionar** para abrir a página **de ligação Adicionar.**

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Ligação site-a-local":::
1. Na página **Adicionar ligação** , configure os valores para a ligação.

   * **Nome:** Atribua um nome à sua ligação.
   * **Tipo de ligação:** Selecione **Site-to-site (IPSec)** .
   * **Gateway de rede virtual:** O valor é fixo porque está a ligar a partir deste gateway.
   * **Gateway de rede local:** **Selecione Escolha um gateway de rede local** e selecione o portal de rede local que pretende utilizar.
   * **Chave Partilhada:** Aqui, o valor tem de corresponder ao valor que está a utilizar para o seu dispositivo VPN local no local. O exemplo utiliza "abc123", mas pode e (deve) utilizar algo mais complexo. O importante é que o valor que especificar aqui seja igual ao valor especificado ao configurar o dispositivo VPN.
   * Deixe **O endereço IP privado Azure** sem ser verificado.
   * Deixar **Ativar o BGP** sem ser verificado.
   * Selecione **IKEv2** .
   * Os restantes valores para **Subscrição** , **Grupo de Recursos** e **Localização** são fixos.

1. Selecione **OK** para criar a sua ligação. Verá *A Criar Ligação* a piscar no ecrã.
1. A ligação é apresentada na página **Ligações** do gateway de rede virtual. O estado será alterado de *Desconhecido* para *A ligar* e, em seguida, *Efetuado com êxito* .
