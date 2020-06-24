---
title: Azure Bastion sessão de monitorização e gestão Microsoft Docs
description: Neste artigo, aprenda a selecionar uma sessão em curso e desconectar ou eliminá-la.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: 5974ebe7960eec1ca3bb8610f66061395fea64d6
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84744107"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitorização e gestão de sessão para Azure Bastion

Uma vez que o serviço Bastion é aloque e implementado na sua rede virtual, pode usá-lo para ligar perfeitamente a qualquer VM nesta rede virtual. À medida que os utilizadores se conectam a cargas de trabalho, o Azure Bastion pode ser usado para monitorizar as sessões remotas e tomar ações de gestão rápida. A monitorização da sessão de Azure Bastion permite-lhe ver a que utilizadores estão ligados aos quais os VMs. Mostra o IP de que o utilizador ligou, quanto tempo esteve ligado e quando se ligou. A experiência de gestão da sessão permite-lhe selecionar uma sessão em curso e desligar a força ou apagar uma sessão de forma a desligar o utilizador da sessão em curso.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitorize sessões remotas

1. No [portal Azure,](https://portal.azure.com)navegue para o seu recurso Azure Bastion e selecione **Sessões** a partir da página Azure Bastion.

   ![sessões](./media/session-monitoring/sessions.png)
2. Na página **'Sessões',** pode ver as sessões remotas em curso no lado direito.

   ![ver sessão](./media/session-monitoring/view-session.png)
3. Selecione **Refresh** para ver a lista atualizada de sessões remotas. Quando selecionar Refresh, Azure Bastion irá buscar as últimas informações de monitorização e a atualizá-la-á no portal.

   ![refresh](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Excluir ou desligar à força uma sessão remota em curso

Pode selecionar um conjunto de sessão e desconectá-las à força. Os seguintes passos mostram-lhe como eliminar sessões remotas:

1. Navegue para o seu recurso Azure Bastion e selecione **Sessões** na página Azure Bastion.

   ![navigate](./media/session-monitoring/navigate.png)
2. Depois de selecionar Sessões, vê uma lista de sessões remotas.

   ![sessões de lista](./media/session-monitoring/list.png)
3. Selecione uma sessão remota específica e, em seguida, selecione as três elipses no lado direito da linha de sessão e, em seguida, selecione **Delete**.

   ![delete](./media/session-monitoring/delete.png)
4. Quando selecionar Eliminar, a sessão remota será desligada e o utilizador será mostrado como uma mensagem "Foi desligado" na sessão remota.

   ![desligar](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião.](bastion-faq.md)
