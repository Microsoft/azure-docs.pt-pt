---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5149973fe63f867b49e55c970779c005e12536b9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780249"
---
1. Abra a página do seu gateway de rede virtual. Existem múltiplas formas de efetuar este procedimento. Você pode navegar até o gateway acessando o **nome de sua VNet-> visão geral-> dispositivos conectados-> nome do seu gateway**.
2. Na página do gateway, clique em **conexões**. No topo da página Ligações, clique em **+Adicionar** para abrir a página **Adicionar ligação**.

   ![Criar uma ligação Site a Site](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Na página **Adicionar ligação**, configure os valores para a ligação.

   - **Nomes** Nomeie sua conexão.
   - **Tipo de conexão:** Selecione **site a site (IPSec)** .
   - **Gateway de rede virtual:** O valor é fixo porque você está se conectando deste gateway.
   - **Gateway de rede local:** Clique em **escolher um gateway de rede local** e selecione o gateway de rede local que você deseja usar.
   - **Chave Partilhada:** Aqui, o valor tem de corresponder ao valor que está a utilizar para o seu dispositivo VPN local no local. O exemplo utiliza "abc123", mas pode e (deve) utilizar algo mais complexo. O importante é que o valor que especificar aqui seja igual ao valor especificado ao configurar o dispositivo VPN.
   - Os restantes valores para **Subscrição**, **Grupo de Recursos** e **Localização** são fixos.

4. Clique em **OK** para criar a ligação. Verá *A Criar Ligação* a piscar no ecrã.
5. A ligação é apresentada na página **Ligações** do gateway de rede virtual. O estado será alterado de *Desconhecido* para *A ligar*e, em seguida, *Efetuado com êxito*.
