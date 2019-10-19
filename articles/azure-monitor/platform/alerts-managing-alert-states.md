---
title: Gerir estados de alertas e de grupos inteligentes
description: Gerenciando os Estados das instâncias de alerta e grupo inteligente
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: fd2a2a4b7d5033ecd35d02baa240bc7586e7a25d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555663"
---
# <a name="manage-alert-and-smart-group-states"></a>Gerir estados de alertas e de grupos inteligentes
Os alertas no Azure Monitor agora têm um [estado de alerta e uma condição de monitor](https://aka.ms/azure-alerts-overview) e, da mesma forma, os grupos inteligentes têm um estado de [grupo inteligente](https://aka.ms/smart-groups). As alterações no estado agora são capturadas no histórico associado ao respectivo alerta ou grupo inteligente. Este artigo orienta você pelo processo de alteração do estado, para um alerta e um grupo inteligente.

## <a name="change-the-state-of-an-alert"></a>Alterar o estado de um alerta
1. Você pode alterar o estado de um alerta das diferentes maneiras a seguir: 
    * Na página todos os alertas, clique na caixa de seleção ao lado dos alertas para os quais você deseja alterar o estado e clique em alterar estado.   
    ![Monitorização](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na página detalhes do alerta de uma instância de alerta específica, você pode clicar em alterar estado   
    ![Monitorização](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na página detalhes do alerta de uma instância de alerta específica, no painel grupo inteligente, você pode clicar na caixa de seleção ao lado dos alertas desejados    
    ![Monitorização](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na página detalhes do grupo inteligente, na lista de alertas de membro, você pode clicar na caixa de seleção ao lado dos alertas para os quais deseja alterar o estado e clicar em alterar estadopara alterar o estado de e clicar em alterar estado.   
    ![Monitorização](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Ao clicar em alterar estado, um pop-up é aberto permitindo que você selecione o estado (novo/confirmado/fechado) e insira um comentário, se necessário.   
![Monitorização](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Quando isso for feito, a alteração de estado será registrada no histórico do respectivo alerta. Isso pode ser exibido abrindo a respectiva página de detalhes e verificando a seção de histórico.    
![Monitorização](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Alterar o estado de um grupo inteligente
1. Você pode alterar o estado de um grupo inteligente das diferentes maneiras a seguir:
    1. Na página de lista de grupos inteligentes, você pode clicar na caixa de seleção ao lado dos grupos inteligentes para os quais deseja alterar o estado e clicar em alterar estado  
    ![Monitorização](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na página de detalhes do grupo inteligente, você pode clicar em alterar estado        
    ![Monitorização](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Ao clicar em alterar estado, um pop-up é aberto permitindo que você selecione o estado (novo/confirmado/fechado) e insira um comentário, se necessário. 
![Monitorização](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Alterar o estado de um grupo inteligente não altera o estado dos alertas de membro individuais.

1. Quando isso for feito, a alteração de estado será registrada no histórico do respectivo grupo inteligente. Isso pode ser exibido abrindo a respectiva página de detalhes e verificando a seção de histórico.     
![Monitorização](./media/alerts-managing-alert-states/state-sg-history.jpg)

