---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5149973fe63f867b49e55c970779c005e12536b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68780249"
---
1. Abra a página para o seu portal de rede virtual. Existem múltiplas formas de efetuar este procedimento. Pode navegar até ao portal indo para o **nome do seu VNet -> visão geral -> dispositivos conectados -> Nome do seu gateway**.
2. Na página para o portal, clique em **Ligações**. No topo da página Ligações, clique em **+Adicionar** para abrir a página **Adicionar ligação**.

   ![Criar uma ligação Site a Site](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Na página **Adicionar ligação**, configure os valores para a ligação.

   - **Nome:** Atribua um nome à sua ligação.
   - **Tipo de ligação:** Selecione **Site a site(IPSec)**.
   - **Gateway de rede virtual:** O valor é fixo porque está a ligar a partir deste gateway.
   - **Gateway de rede local:** Clique em **Escolher um gateway de rede local** e selecione o gateway de rede local que pretende utilizar.
   - **Chave Partilhada:** Aqui, o valor tem de corresponder ao valor que está a utilizar para o seu dispositivo VPN local no local. O exemplo utiliza "abc123", mas pode e (deve) utilizar algo mais complexo. O importante é que o valor que especificar aqui seja igual ao valor especificado ao configurar o dispositivo VPN.
   - Os restantes valores para **Subscrição**, **Grupo de Recursos** e **Localização** são fixos.

4. Clique em **OK** para criar a ligação. Verá *A Criar Ligação* a piscar no ecrã.
5. A ligação é apresentada na página **Ligações** do gateway de rede virtual. O estado será alterado de *Desconhecido* para *A ligar*e, em seguida, *Efetuado com êxito*.
