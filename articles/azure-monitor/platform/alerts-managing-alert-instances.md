---
title: Gerir casos de alerta no Azure Monitor
description: Gerir casos de alerta em Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 82905cba5f391365ada13f4e5df5ad139f4c121e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102875"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Gerir instâncias de alerta com alertas unificados

Com a [experiência de alertas unificados](./alerts-overview.md) no Azure Monitor, você pode ver todos os seus diferentes tipos de alertas em Azure. Isto abrange várias subscrições, num único painel. Este artigo mostra como pode ver as suas instâncias de alerta e como encontrar casos específicos de alerta para a resolução de problemas.

> [!NOTE]
> Só é possível aceder aos alertas gerados nos últimos 30 dias.

## <a name="go-to-the-alerts-page"></a>Vá à página de alertas

Pode ir à página de alertas de qualquer das seguintes formas:

- No [portal Azure,](https://portal.azure.com/)selecione **Monitor**  >  **Alertas de**Monitor .  

     ![Screenshot dos alertas do monitor](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Utilize o contexto de um recurso específico. Abrir um recurso, ir à secção **de Monitorização** e escolher **Alertas**. A página de aterragem é pré-filtrada para alertas sobre esse recurso específico.

     ![Screenshot dos alertas de monitorização de recursos](media/alerts-managing-alert-instances/alert-resource.JPG)

- Utilize o contexto de um grupo de recursos específicos. Abrir um grupo de recursos, ir à secção **de Monitorização** e escolher **Alertas**. A página de aterragem é pré-filtrada para alertas nesse grupo de recursos específicos.    

     ![Screenshot dos alertas de monitorização do grupo de recursos](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Encontre instâncias de alerta

A página **'Resumo alerta'** dá-lhe uma visão geral de todas as suas instâncias de alerta em Azure. Pode modificar a vista sumária selecionando **várias subscrições** (até um máximo de 5), ou filtrando entre grupos de **recursos,** **recursos**específicos ou **intervalos de tempo**. Selecione **Total Alerts**, ou qualquer uma das bandas de severidade, para ir à vista da lista para os seus alertas.     

![Screenshot da página de resumo de alertas](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Na página **All Alerts,** todos os casos de alerta em Azure estão listados. Se estiver a chegar ao portal a partir de uma notificação de alerta, pode utilizar os filtros disponíveis para se estreitar nessa instância de alerta específica.

> [!NOTE]
> Se tiver chegado à página selecionando qualquer uma das bandas de severidade, a lista é pré-filtrada para essa gravidade.

Além dos filtros disponíveis na página anterior, também pode filtrar com base no serviço de monitorização (por exemplo, plataforma para métricas), condição de monitor (disparado ou resolvido), gravidade, estado de alerta (novo/reconhecido/fechado) ou no ID do grupo inteligente.

![Screenshot da página de todos os alertas](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Se tiver chegado à página selecionando qualquer uma das bandas de severidade, a lista é pré-filtrada para essa gravidade.

A seleção de qualquer instância de alerta abre a página Detalhes de **Alerta,** permitindo-lhe ver mais detalhes sobre essa instância de alerta específica.   

![Screenshot da página detalhes de alerta](media/alerts-managing-alert-instances/alert-details.jpg)