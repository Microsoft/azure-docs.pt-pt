---
title: Gerir instâncias de alertas
description: Gestão das instâncias do alerta em todo o Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: cb93f38c05156d7ab5acb89ffff810949583e507
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089408"
---
# <a name="manage-alert-instances"></a>Gerir instâncias de alertas
Com o [experiência de alertas unificada](https://aka.ms/azure-alerts-overview) no Azure Monitor, agora, pode ver todos os diferentes tipos de alertas no Azure, que abrange várias subscrições, num único painel de vidro. Este artigo explica-lhe como pode ver as instâncias de alerta e como aprofundada sobre o portal para encontrar instâncias específicas de alerta de resolução de problemas.

1. Existem três formas de direcionado para a página de alertas

   + Na [portal](https://portal.azure.com/), selecione **Monitor** e, na secção Monitor - escolher **alertas**.  
     ![Monitorização](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Pode navegar para alertas a partir de dentro do contexto de um específico **recursos**. Depois de um recurso for aberto, navegue por meio de sua tabela de conteúdos para a secção de monitorização e escolha **alertas**, com a página de aterrissagem previamente a ser filtrada para alertas nesse recurso específico.
   
     ![Monitorização](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + Pode navegar para alertas a partir de dentro do contexto de um específico **grupo de recursos**. Depois de um grupo de recursos é aberto, navegue por meio de sua tabela de conteúdos para a secção de monitorização e escolha **alertas**, com a página de destino que está a ser previamente filtrada para os alertas no grupo de recursos específico.    
   
     ![Monitorização](media/alerts-managing-alert-instances/alert-rg.JPG)

1. Será direcionado para o **resumo de alertas** página, o que lhe dá uma visão geral de todas as instâncias de alerta em todo o Azure. Pode modificar a vista de resumo, selecionando **várias subscrições** (um máximo de 5) ou ao filtrar em **grupos de recursos**específico **recursos**, ou **intervalos de tempo**. Clique no Total de alertas ou qualquer uma das bandas de gravidade para ir para a vista de lista para os alertas.     
   ![Resumo de alertas](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. Será direcionado para o **todos os alertas** página, onde poderá ver todas as instâncias de alerta em todo o Azure listado. Se estiver vindo para o portal de uma notificação de alerta, pode utilizar os filtros disponíveis para restringir essa instância de alerta específica. (**Nota**: se veio até a página clicando em qualquer uma das bandas de gravidade, a lista será previamente filtrada para esse gravidade quando é apresentado). Para além dos filtros disponíveis na página anterior, agora também pode filtrar com base no monitor de serviço (por exemplo, a plataforma para métricas), condição do monitor (disparado ou resolvidos), gravidade, estado de alerta (novo/confirmados/fechado) ou o ID do grupo inteligente.

   ![Todos os Alertas](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Se veio para a página clicando em qualquer uma das bandas de gravidade, a lista será previamente filtrada para esse gravidade, quando é apresentado nesta página.
 
1. Clicar em qualquer instância de alerta abre o **detalhes do alerta** página, permitindo que aprofundadas para obter informações sobre essa instância de alerta específica.   
   ![Detalhes do alerta](media/alerts-managing-alert-instances/alert-details.jpg)  

