---
title: Gerir estados de alertas e de grupos inteligentes
description: Gerir os estados dos casos de grupo sinuoso e inteligente
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667521"
---
# <a name="manage-alert-and-smart-group-states"></a>Gerir estados de alertas e de grupos inteligentes

Os alertas no Monitor Azure têm agora um estado de [alerta e uma condição](https://aka.ms/azure-alerts-overview) de monitor e, da mesma forma, os Smart Groups têm um estado de grupo [inteligente.](https://aka.ms/smart-groups) As mudanças no estado são agora capturadas na história associada ao respetivo grupo de alerta ou inteligente. Este artigo acompanha-o através do processo de mudança de estado, tanto para um alerta como para um grupo inteligente.

## <a name="change-the-state-of-an-alert"></a>Mude o estado de um alerta

1. Pode alterar o estado de um alerta das seguintes formas: 
    * Na página All Alerts, clique na caixa de verificação ao lado dos alertas que pretende alterar o estado e clique em alterar o estado.   
    ![Monitorização](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na página Detalhes de Alerta para uma determinada instância de alerta, pode clicar em alterar o estado   
    ![Monitorização](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na página Dedados de Alerta para uma instância de alerta específica, no painel do Smart Group pode clicar na caixa de verificação ao lado dos alertas que deseja    
    ![Monitorização](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na página Dedetalhes do Grupo Inteligente, na lista de alertas de membros pode clicar na caixa de verificação ao lado dos alertas que pretende alterar o estado e clicar em Alterar o Estado de Mudança para alterar o estado de mudança e clicar em Alterar o Estado de Mudança.   
    ![Monitorização](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Ao clicar no Change State, abre-se um popup que lhe permite selecionar o estado (Novo/Reconhecido/Fechado) e introduzir um comentário se necessário.   
![Monitorização](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Uma vez feito isto, a mudança de Estado é registada na história do respetivo alerta. Isto pode ser visto abrindo a página respetivo detalhes e verificando a secção de histórico.    
![Monitorização](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Mudar o estado de um grupo inteligente
1. Pode alterar o estado de um grupo inteligente das seguintes formas:
    1. Na página da lista do Smart Group, pode clicar na caixa de verificação ao lado dos grupos inteligentes que deseja alterar o estado e clicar em Change State  
    ![Monitorização](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na página de Detalhes do Grupo Inteligente, pode clicar em alterar o estado        
    ![Monitorização](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Ao clicar no Change State, abre-se um popup que lhe permite selecionar o estado (Novo/Reconhecido/Fechado) e introduzir um comentário se necessário. 
![Monitorização](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Mudar o estado de um grupo inteligente não altera o estado dos alertas individuais.

1. Uma vez feito isto, a mudança de Estado é registada na história do respetivo grupo inteligente. Isto pode ser visto abrindo a página respetivo detalhes e verificando a secção de histórico.     
![Monitorização](./media/alerts-managing-alert-states/state-sg-history.jpg)

