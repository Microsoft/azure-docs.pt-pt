---
title: Gerir instâncias de alertas
description: Gerenciando instâncias de alerta no Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: dbc7d37ce311d203ee051b5a23632d2bf401e27a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034832"
---
# <a name="manage-alert-instances"></a>Gerir instâncias de alertas
Com a [experiência de alertas](https://aka.ms/azure-alerts-overview) unificados no Azure monitor, agora você pode ver todos os seus diferentes tipos de alertas no Azure, abrangendo várias assinaturas, em um único painel de vidro. Este artigo explica como você pode exibir suas instâncias de alerta e como aprofundar-se no portal para encontrar instâncias de alerta específicas para solução de problemas.

> [!NOTE]
   >  Somente os alertas gerados nos últimos 30 dias podem ser acessados na UX ou por meio das APIs REST.

1. Há três maneiras de chegar à página de alertas

   + No [portal](https://portal.azure.com/), selecione **Monitor** e, na seção monitor, escolha **alertas**.  
     ![Monitorização](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Você pode navegar até alertas de dentro do contexto de um **recurso**específico. Quando um recurso for aberto, navegue pelo seu Sumário para a seção monitoramento e escolha **alertas**, com a página de aterrissagem filtrada previamente para alertas nesse recurso específico.
   
     ![Monitorização](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + Você pode navegar até alertas de dentro do contexto de um **grupo de recursos**específico. Depois que um grupo de recursos for aberto, navegue pelo seu Sumário para a seção monitoramento e escolha **alertas**, com a página de aterrissagem filtrada previamente para alertas nesse grupo de recursos específico.    
   
     ![Monitorização](media/alerts-managing-alert-instances/alert-rg.JPG)

1. Você chegará à página de **Resumo de alertas** , que fornece uma visão geral de todas as suas instâncias de alerta no Azure. Você pode modificar o modo de exibição de resumo selecionando **várias assinaturas** (no máximo 5) ou filtrando entre **grupos de recursos**, **recursos**específicos ou **intervalos de tempo**. Clique em um total de alertas ou em qualquer uma das faixas de severidade para ir para a exibição de lista para seus alertas.     
   ![Resumo de alertas](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. Você vai para a página **todos os alertas** , em que verá todas as instâncias de alerta no Azure listadas. Se você estiver entrando no portal de uma notificação de alerta, poderá usar os filtros disponíveis para restringir essa instância de alerta específica. (**Observação**: se você chegou à página clicando em qualquer uma das faixas de severidade, a lista será previamente filtrada para essa gravidade quando você chegar). Além dos filtros disponíveis na página anterior, agora você também pode filtrar a base do serviço monitor (por exemplo, plataforma para métricas), condição do monitor (disparado ou resolvido), severidade, estado do alerta (novo/confirmado/fechado) ou a ID do grupo inteligente.

   ![Todos os Alertas](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Se você vier à página clicando em qualquer uma das faixas de severidade, a lista será previamente filtrada para essa gravidade quando você chegar nessa página.
 
1. Clicar em qualquer instância de alerta abre a página **detalhes do alerta** , permitindo que você Aprofunde-se nas informações sobre essa instância de alerta específica.   
   ![Detalhes do alerta](media/alerts-managing-alert-instances/alert-details.jpg)  

