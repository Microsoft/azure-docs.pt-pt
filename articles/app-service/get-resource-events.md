---
title: Obtenha eventos de recursos no Serviço de Aplicações Azure
description: Saiba como obter eventos de recursos através de Registos de Atividades e Grelha de Eventos na sua aplicação App Service.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 7075e3eacc85198e22a9aa0e53f67a22416b2678
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649067"
---
# <a name="get-resource-events-in-azure-app-service"></a>Obtenha eventos de recursos no Serviço de Aplicações Azure

O Azure App Service fornece ferramentas incorporadas para monitorizar o estado e a saúde dos seus recursos. Os eventos de recursos ajudam-no a compreender quaisquer alterações que tenham sido feitas aos recursos da sua aplicação web subjacentes e tomem medidas necessárias. Os exemplos do evento incluem: escala de instâncias, atualizações para definições de aplicações, reinício da aplicação web, e muito mais. Neste artigo, você aprenderá a ver registos de atividade do Azure e permitirá que a [Rede de Eventos](https://docs.microsoft.com/azure/event-grid/) monitorize [eventos](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) relacionados com a sua aplicação web do App Service.

> [!NOTE]
> A integração do Serviço de Aplicações com a Grelha de Eventos está em **pré-visualização.** [Veja o anúncio para mais detalhes.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Ver Registos de Atividade sinuosa do Azure
Os Registos de Atividade do Azure contêm eventos de recursos emitidos por operações tomadas sobre os recursos da sua subscrição. Tanto as ações dos utilizadores nos modelos do portal Azure como do Gestor de Recursos Azure contribuem para os eventos capturados pelo registo de Atividades. 

Registos de Atividade sinuosos para detalhes do Serviço de Aplicações, tais como:
- que operações foram tomadas sobre os recursos (ex: Planos de Serviço de Aplicações)
- que iniciou a operação
- quando a operação ocorreu
- o estado da operação
- valores de propriedade para ajudá-lo a pesquisar a operação

### <a name="what-can-you-do-with-azure-activity-logs"></a>O que pode fazer com os Registos de Atividade seleção de Azure?

Os registos de atividade do Azure podem ser consultados através do portal Azure, PowerShell, REST API ou CLI. Pode enviar os registos para uma conta de armazenamento, Event Hub e Log Analytics. Também pode analisá-los no Power BI ou criar alertas para se manterem atualizados sobre eventos de recursos.

[Ver e recuperar eventos de log da Atividade Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>Registos de atividade seleção para grelha de eventos

Embora os registos de Atividade sejam baseados no utilizador, existe uma nova integração da Rede de [Eventos](https://docs.microsoft.com/azure/event-grid/) com o App Service (pré-visualização) que regista tanto as ações dos utilizadores como os eventos automatizados. Com a Grelha de Eventos, pode configurar um manipulador para reagir aos referidos eventos. Por exemplo, utilize o Event Grid para acionar instantaneamente uma função sem servidor para executar imagens de análises sempre que é adicionada uma fotografia nova a um contentor de armazenamento de blobs.

Em alternativa, pode utilizar o Event Grid com o Logic Apps para processar dados em qualquer local sem ter de escrever código. O Event Grid liga as origens de dados e os processadores de eventos. Por exemplo, utilize o Event Grid para acionar instantaneamente uma função sem servidor para executar imagens de análises sempre que é adicionada uma fotografia nova a um contentor de armazenamento de blobs.

[Veja as propriedades e o esquema para eventos de serviço de aplicações azure.](https://docs.microsoft.com/azure/event-grid/event-schema-app-service)

## <a name="next-steps"></a><a name="nextsteps"></a>Próximos passos
* [Registos de consulta com monitor Azure](../azure-monitor/log-query/log-query-overview.md)
* [Como monitorizar o Serviço de Aplicações Azure](web-sites-monitor.md)
* [Troubleshooting Azure App Service em Estúdio Visual](troubleshoot-dotnet-visual-studio.md)
* [Analisar registos de aplicativos no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
