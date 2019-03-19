---
title: Descrição geral de contentores do Azure da monitorização | Documentos da Microsoft
description: Este artigo fornece uma descrição geral dos diferentes métodos disponíveis no Azure para monitorizar contentores no Azure para compreender rapidamente um Estado de funcionamento de clusters e disponibilidade.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: d137576b4beb5cf36dce99ffb1869049f37b60b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881270"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Descrição geral da monitorização de contentores no Azure
Com o Azure, pode efetivamente de monitorizar e gerir as suas cargas de trabalho implementadas em contentores do Azure com o Docker ou o Kubernetes. É importante compreender o desempenho de contentores com várias aplicações de microsserviços para fornecer um serviço fiável em escala e suporta o seu plano de monitoramento. Este artigo fornece uma breve descrição geral da gestão e capacidades de monitorização no Azure para ajudar a compreendê-los e que são adequadas com base nos seus requisitos.

Usando [Monitor do Azure para contentores](container-insights-overview.md), pode ver o desempenho e estado de funcionamento da sua infraestrutura de contentor do Linux rapidamente e investigar problemas rapidamente. A telemetria é armazenada numa área de trabalho do Log Analytics e integrado no portal do Azure, onde poderá explorar, filtrar e segmento de dados agregados com dashboards para manter a par de carga, desempenho e estado de funcionamento.  

Para os contentores em execução fora o serviço alojado do Kubernetes do Azure, o Log Analytics [solução Windows e o contentor do Docker](../../azure-monitor/insights/containers.md) ajuda-o ver e gerir os anfitriões de contentor do Windows e a Docker. Na área de trabalho do Log Analytics, pode ver detalhes de inventário, desempenho e eventos a partir de nós e os contentores no ambiente. Pode ver informações detalhadas de auditoria que mostra os comandos utilizados com contentores e pode resolver contentores ao visualizar e procurar registos centralizados sem ter de aceder remotamente a anfitriões do Docker ou do Windows.

Para alcançar holística ou de monitorização do aplicativo ponto a ponto, qualquer dependência se é do Azure ou recurso, no local deve ser monitorizada com o Azure Monitor ou o Log Analytics.  A camada de aplicativo deve ser incluída para poder adicionar uma camada adicional de reconhecimento de estado de funcionamento, tanto no nível de plataforma e da aplicação com o Application Insights. No nível da plataforma, existem SDKs do Application Insights para [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), e [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Para aplicações de microsserviços, há suporte para [Java](../../azure-monitor/app/java-get-started.md), [node. js](../../azure-monitor/learn/nodejs-quick-start.md), [.NET](../../azure-monitor/app/asp-net.md), [.NET Core](../../azure-monitor/app/asp-net-core.md), bem como várias outras [idiomas/estruturas](../../azure-monitor/app/platforms.md). 

Caso contrário, os problemas serão enviadas não identificados que podem afetar a disponibilidade da aplicação e destinos de nível de serviço não serão atendidos.  
