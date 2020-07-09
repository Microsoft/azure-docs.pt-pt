---
title: Gerir estados de alertas e de grupos inteligentes
description: Gerir os estados das instâncias de alerta e grupo inteligente
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77667521"
---
# <a name="manage-alert-and-smart-group-states"></a>Gerir estados de alertas e de grupos inteligentes

Os alertas no Azure Monitor têm agora um [estado de alerta e uma condição de monitor](https://aka.ms/azure-alerts-overview) e, da mesma forma, os Grupos Inteligentes têm um estado de grupo [inteligente](https://aka.ms/smart-groups). As alterações ao estado são agora capturadas na história associadas ao respetivo grupo de alerta ou inteligente. Este artigo acompanha-o através do processo de mudança de estado, tanto para um alerta como para um grupo inteligente.

## <a name="change-the-state-of-an-alert"></a>Alterar o estado de alerta

1. Pode alterar o estado de alerta de diferentes formas: 
    * Na página 'Todos os Alertas', clique na caixa de verificação ao lado dos alertas de que deseja alterar o estado de e clique em alterar o estado.   
    ![Monitorização](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na página Detalhes de Alerta para uma determinada instância de alerta, pode clicar em alterar estado   
    ![Monitorização](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na página Detalhes de Alerta para uma instância de alerta específica, no painel do Grupo Inteligente pode clicar na caixa de verificação ao lado dos alertas que deseja    
    ![Monitorização](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na página Smart Group Details, na lista de alertas de membros pode clicar na caixa de verificação ao lado dos alertas que pretende alterar o estado de e clicar em Alterar Estado para alterar o estado de e clicar em Alterar Estado.   
    ![Monitorização](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Ao clicar em Change State, abre-se um popup permitindo-lhe selecionar o estado (Novo/Reconhecido/Fechado) e introduzir um comentário se necessário.   
![Monitorização](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Uma vez feito isto, a mudança de estado é registada na história do respetivo alerta. Isto pode ser visto abrindo a respetiva página Detalhes e verificando a secção de histórico.    
![Monitorização](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Mudar o estado de um grupo inteligente
1. Pode alterar o estado de um grupo inteligente das seguintes formas:
    1. Na página da lista do Grupo Inteligente, pode clicar na caixa de verificação ao lado dos grupos inteligentes que deseja alterar o estado de e clicar em Alterar Estado  
    ![Monitorização](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na página Detalhes do Grupo Inteligente, pode clicar em alterar estado        
    ![Monitorização](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Ao clicar em Change State, abre-se um popup permitindo-lhe selecionar o estado (Novo/Reconhecido/Fechado) e introduzir um comentário se necessário. 
![Monitorização](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Mudar o estado de um grupo inteligente não altera o estado dos alertas individuais dos membros.

1. Uma vez feito isto, a mudança de estado é registada na história do respetivo grupo inteligente. Isto pode ser visto abrindo a respetiva página Detalhes e verificando a secção de histórico.     
![Monitorização](./media/alerts-managing-alert-states/state-sg-history.jpg)

