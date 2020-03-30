---
title: Monitorização e gestão da sessão azure Bastião Microsoft Docs
description: Neste artigo, aprenda a selecionar uma sessão em curso e a desconectá-la ou eliminá-la.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76981094"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitorização e gestão de sessões para o Bastião Azure

Uma vez que o serviço Bastion é aprovisionado e implantado na sua rede virtual, pode usá-lo para ligar perfeitamente a qualquer VM nesta rede virtual. À medida que os utilizadores se ligam às cargas de trabalho, o Azure Bastion pode ser usado para monitorizar as sessões remotas e tomar ações de gestão rápidas. A monitorização da sessão azure Bastion permite-lhe visualizar quais os utilizadores ligados aos quais VMs. Mostra o IP de que o utilizador ligou, quanto tempo estão ligados e quando se conectam. A experiência de gestão da sessão permite selecionar uma sessão em curso e desligar a força ou apagar uma sessão de forma a desligar o utilizador da sessão em curso.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitorize sessões remotas

1. No [portal Azure,](https://portal.azure.com)navegue para o seu recurso Azure Bastion e selecione **Sessions** a partir da página Do Bastião Azure.

   ![sessões](./media/session-monitoring/sessions.png)
2. Na página **Sessions,** pode ver as sessões remotas em curso no lado direito.

   ![ver sessão](./media/session-monitoring/view-session.png)
3. Selecione **Refresh** para ver a lista atualizada de sessões remotas. Quando selecionar refresh, o Azure Bastion irá buscar as últimas informações de monitorização e refresca-as no portal.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Ativar a porta 4443 para o tráfego de entrada do Gateway Manager para a monitorização da sessão funcionar.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Eliminar ou desligar a força de uma sessão remota em curso

Pode selecionar um conjunto de sessões e desconectá-las à força. Os seguintes passos mostram-lhe como eliminar sessões remotas:

1. Navegue para o seu recurso Azure Bastion e selecione **Sessões** a partir da página Do Bastião Azure.

   ![navigate](./media/session-monitoring/navigate.png)
2. Depois de selecionar As Sessões, consulte uma lista de sessões remotas.

   ![sessões de lista](./media/session-monitoring/list.png)
3. Selecione uma sessão remota específica e, em seguida, selecione as três elipses na extremidade direita da linha de sessão e, em seguida, **selecione Delete**.

   ![delete](./media/session-monitoring/delete.png)
4. Quando selecionar Apagar, a sessão remota será desligada e o utilizador será mostrado uma mensagem "Foi desligado" na sessão remota.

   ![desligar](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Passos seguintes

Leia o [Bastião FAQ.](bastion-faq.md)