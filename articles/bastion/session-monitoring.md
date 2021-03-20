---
title: Azure Bastion sessão de monitorização e gestão | Microsoft Docs
description: Neste artigo, aprenda a selecionar uma sessão em curso e desconectar ou eliminá-la.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: beb4f8fc5b9e22bc3e2bdd4732c8321dfcd70780
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90980722"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitorização e gestão de sessão para Azure Bastion

Uma vez que o serviço Bastion é aloque e implementado na sua rede virtual, pode usá-lo para ligar perfeitamente a qualquer VM nesta rede virtual. À medida que os utilizadores se conectam a cargas de trabalho, o Azure Bastion pode ser usado para monitorizar as sessões remotas e tomar ações de gestão rápida. A monitorização de sessões do Azure Bastion permite-lhe ver que utilizadores estão ligados a que VMs. Mostra o IP de que o utilizador ligou, quanto tempo esteve ligado e quando se ligou. A experiência de gestão da sessão permite-lhe selecionar uma sessão em curso e desligar a força ou apagar uma sessão de forma a desligar o utilizador da sessão em curso.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitorize sessões remotas

1. No [portal Azure,](https://portal.azure.com)navegue para o seu recurso Azure Bastion e selecione **Sessões** a partir da página Azure Bastion.

   ![O screenshot mostra as definições do menu do portal Azure com sessões selecionadas.](./media/session-monitoring/sessions.png)
2. Na página **'Sessões',** pode ver as sessões remotas em curso no lado direito.

   ![ver sessão](./media/session-monitoring/view-session.png)
3. Selecione **Refresh** para ver a lista atualizada de sessões remotas. Quando selecionar Refresh, Azure Bastion irá buscar as últimas informações de monitorização e a atualizá-la-á no portal.

   ![O Screenshot mostra uma sessão que utiliza O Bastião Azure com a opção de atualização realçada.](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Excluir ou desligar à força uma sessão remota em curso

Pode selecionar um conjunto de sessão e desconectá-las à força. Os seguintes passos mostram-lhe como eliminar sessões remotas:

1. Navegue para o seu recurso Azure Bastion e selecione **Sessões** na página Azure Bastion.

   ![A screenshot mostra o portal Azure com sessões selecionadas em Definições.](./media/session-monitoring/navigate.png)
2. Depois de selecionar Sessões, vê uma lista de sessões remotas.

   ![sessões de lista](./media/session-monitoring/list.png)
3. Selecione uma sessão remota específica e, em seguida, selecione as três elipses no lado direito da linha de sessão e, em seguida, selecione **Delete**.

   ![A screenshot mostra o portal Azure exibindo uma sessão com o ícone Delete selecionado.](./media/session-monitoring/delete.png)
4. Quando selecionar Eliminar, a sessão remota será desligada e o utilizador será mostrado como uma mensagem "Foi desligado" na sessão remota.

   ![A screenshot mostra uma mensagem a verificar se foi desligado.](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião.](bastion-faq.md)
