---
title: Monitoramento e gerenciamento de sessão de bastiões do Azure | Microsoft Docs
description: Neste artigo, saiba como selecionar uma sessão em andamento e forçar a desconexão ou exclusão.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: a4a97ebd0e44bfd3b0ee167a2f3a7da435ac5087
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513006"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitoramento e gerenciamento de sessão para bastiões do Azure

Depois que o serviço de bastiões for provisionado e implantado em sua rede virtual, você poderá usá-lo para se conectar diretamente a qualquer VM nessa rede virtual. À medida que os usuários se conectam a cargas de trabalho, a bastiões do Azure pode ser usada para monitorar as sessões remotas e fazer ações de gerenciamento rápido. O monitoramento de sessão de bastiões do Azure permite exibir quais usuários estão conectados a quais VMs. Ele mostra o IP ao qual o usuário se conectou, por quanto tempo eles foram conectados e quando eles se conectaram. A experiência de gerenciamento de sessão permite que você selecione uma sessão em andamento e force a desconexão ou exclua uma sessão para desconectar o usuário da sessão em andamento.

## <a name="monitor"></a>Monitorar sessões remotas

1. Na [portal do Azure](https://portal.azure.com), navegue até o recurso de bastiões do Azure e selecione **sessões** na página de bastiões do Azure.

   ![das](./media/session-monitoring/sessions.png)
2. Na página **sessões** , você pode ver as sessões remotas em andamento no lado direito.

   ![Exibir sessão](./media/session-monitoring/view-session.png)
3. Selecione **Atualizar** para ver a lista atualizada de sessões remotas. Quando você seleciona atualizar, a bastiões do Azure buscará as informações de monitoramento mais recentes e as atualizará no Portal.

   ![Nova](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Habilite a porta 4443 para o tráfego de entrada do Gerenciador de gateway para que o monitoramento de sessão funcione.
>

## <a name="view"></a>Excluir ou forçar a desconexão de uma sessão remota em andamento

Você pode selecionar um conjunto de sessões e forçar a desconexão. As etapas a seguir mostram como excluir sessões remotas:

1. Navegue até o recurso de bastiões do Azure e selecione **sessões** na página de bastiões do Azure.

   ![Navegue até](./media/session-monitoring/navigate.png)
2. Depois de selecionar sessões, você verá uma lista de sessões remotas.

   ![listar sessões](./media/session-monitoring/list.png)
3. Selecione uma sessão remota específica e, em seguida, selecione as três reticências na extremidade direita da linha da sessão e, em seguida, selecione **excluir**.

   ![delete](./media/session-monitoring/delete.png)
4. Quando você seleciona excluir, a sessão remota será desconectada e o usuário receberá uma mensagem "você foi desconectado" na sessão remota.

   ![Desligar](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Passos seguintes

Leia as [perguntas frequentes sobre bastiões](bastion-faq.md).