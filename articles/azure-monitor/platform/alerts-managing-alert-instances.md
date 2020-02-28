---
title: Gerir casos de alerta no Monitor Azure
description: Gestão de casos de alerta em Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667623"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Gerir casos de alerta com alertas unificados

Com a experiência de [alertas unificados](https://aka.ms/azure-alerts-overview) no Monitor Azure, pode ver todos os seus diferentes tipos de alertas em azure. Isto abrange várias assinaturas, num único painel. Este artigo mostra como pode ver os seus casos de alerta e como encontrar casos específicos de alerta para resolução de problemas.

> [!NOTE]
> Só é possível aceder a alertas gerados nos últimos 30 dias.

## <a name="go-to-the-alerts-page"></a>Vá para a página de alertas

Pode ir à página de alertas de qualquer uma das seguintes formas:

- No [portal Azure,](https://portal.azure.com/)selecione **Monitor** > **Alertas**.  

     ![Screenshot dos alertas de monitor](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Use o contexto de um recurso específico. Abra um recurso, vá à secção **de Monitorização** e escolha **Alertas**. A página de aterragem é pré-filtrada para alertas sobre esse recurso específico.

     ![Screenshot de alertas de monitorização de recursos](media/alerts-managing-alert-instances/alert-resource.JPG)

- Utilize o contexto de um grupo de recursos específicos. Abra um grupo de recursos, vá à secção **de Monitorização** e escolha **Alertas**. A página de aterragem é pré-filtrada para alertas nesse grupo de recursos específicos.    

     ![Screenshot dos alertas de monitorização do grupo de recursos](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Encontre casos de alerta

A página **Sumária alerta** dá-lhe uma visão geral de todos os seus casos de alerta em Azure. Pode modificar a visão sumária selecionando **várias subscrições** (até um máximo de 5), ou filtrando grupos de **recursos,** **recursos específicos**ou intervalos de **tempo.** Selecione **Total Alerts**, ou qualquer uma das bandas de gravidade, para ir à vista da lista para os seus alertas.     

![Screenshot da página sumária de alertas](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Na página **All Alerts,** todos os casos de alerta em Azure estão listados. Se estiver a chegar ao portal a partir de uma notificação de alerta, pode utilizar os filtros disponíveis para se restringir nessa instância de alerta específica.

> [!NOTE]
> Se veio à página selecionando qualquer uma das bandas de gravidade, a lista é pré-filtrada para essa gravidade.

Além dos filtros disponíveis na página anterior, também pode filtrar com base no serviço de monitor (por exemplo, plataforma para métricas), condição de monitor (disparado ou resolvido), gravidade, estado de alerta (novo/reconhecido/fechado) ou o ID do grupo inteligente.

![Screenshot da página de todos os alertas](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Se veio à página selecionando qualquer uma das bandas de gravidade, a lista é pré-filtrada para essa gravidade.

Selecionando qualquer instância de alerta abre a página Dados de **Alerta,** permitindo-lhe ver mais detalhes sobre essa instância de alerta específica.   

![Screenshot da página de Detalhes de Alerta](media/alerts-managing-alert-instances/alert-details.jpg)  

