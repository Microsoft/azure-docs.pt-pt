---
title: Obtenha eventos de recursos no Azure App Service
description: Saiba como obter eventos de recursos através de Registos de Atividade e Grade de Eventos na sua aplicação de Serviço de Aplicações.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c20028a4f84dae9d292cf855a1e164bd69864909
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574047"
---
# <a name="get-resource-events-in-azure-app-service"></a>Obtenha eventos de recursos no Azure App Service

O Azure App Service fornece ferramentas incorporadas para monitorizar o estado e a saúde dos seus recursos. Os eventos de recursos ajudam-no a compreender quaisquer alterações que foram feitas aos recursos da sua aplicação web subjacente e a tomar medidas conforme necessário. Exemplos de eventos incluem: dimensionamento de instâncias, atualizações para definições de aplicações, reinicio da aplicação web, e muito mais. Neste artigo, você vai aprender a ver [Registos de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log) e permitir que a [Grade de Eventos](../event-grid/index.yml) monitorize eventos de recursos relacionados com a sua aplicação web do Serviço de Aplicações.

> [!NOTE]
> A integração do Serviço de Aplicações com a Grade de Eventos está em **pré-visualização.** [Veja o anúncio para mais detalhes.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Ver Registos de Atividades Azure
Os Registos de Atividades Azure contêm eventos de recursos emitidos pelas operações tomadas sobre os recursos na sua subscrição. Tanto as ações do utilizador no portal Azure como os modelos Azure Resource Manager contribuem para os eventos capturados pelo registo de Atividade. 

Registos de atividades Azure para detalhes do Serviço de Aplicações, tais como:
- que operações foram tomadas sobre os recursos (ex: Planos de Serviço de Aplicações)
- que iniciou a operação
- quando a operação ocorreu
- o estado da operação
- valores de propriedade para ajudá-lo a pesquisar a operação

### <a name="what-can-you-do-with-azure-activity-logs"></a>O que pode fazer com os Registos de Atividades Azure?

Os Registos de Atividade Azure podem ser consultados utilizando o portal Azure, PowerShell, REST API ou CLI. Pode enviar os registos para uma conta de armazenamento, Event Hub e Log Analytics. Também pode analisá-los no Power BI ou criar alertas para se manter atualizado sobre eventos de recursos.

[Ver e recuperar eventos de registo de atividades Azure.](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>Troncos de atividade do navio para a grelha de eventos

Embora os registos de atividade sejam baseados no utilizador, há uma nova integração de [Event Grid](../event-grid/index.yml) com o App Service (pré-visualização) que regista as ações do utilizador e eventos automatizados. Com a Grade de Eventos, pode configurar um manipulador para reagir aos referidos eventos. Por exemplo, utilize o Event Grid para acionar instantaneamente uma função sem servidor para executar imagens de análises sempre que é adicionada uma fotografia nova a um contentor de armazenamento de blobs.

Em alternativa, pode utilizar o Event Grid com o Logic Apps para processar dados em qualquer local sem ter de escrever código. O Event Grid liga as origens de dados e os processadores de eventos. Por exemplo, utilize o Event Grid para acionar instantaneamente uma função sem servidor para executar imagens de análises sempre que é adicionada uma fotografia nova a um contentor de armazenamento de blobs.

[Ver as propriedades e esquema para eventos de serviço de aplicações Azure.](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> Próximos passos
* [Registos de consulta com monitor Azure](../azure-monitor/logs/log-query-overview.md)
* [Como monitorizar o Serviço de Aplicações Azure](web-sites-monitor.md)
* [Serviço de aplicações Azure em Estúdio Visual](troubleshoot-dotnet-visual-studio.md)
* [Analisar registos de aplicativos em HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)