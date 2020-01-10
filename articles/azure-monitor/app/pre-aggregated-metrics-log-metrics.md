---
title: Métricas baseadas em log e previamente agregadas no Aplicativo Azure insights | Microsoft Docs
description: Por que usar métricas com base em log versus agregadas previamente no Aplicativo Azure insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 384e3c9032b324ee92762db9156c628a05e5e862
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406583"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Métricas baseadas no registo e pré-agregadas no Application Insights

Este artigo explica a diferença entre as métricas de Application Insights "tradicionais" que são baseadas em logs e métricas previamente agregadas que estão atualmente em visualização pública. Os dois tipos de métricas estão disponíveis para os usuários de Application Insights, e cada uma traz um valor exclusivo no monitoramento da integridade do aplicativo, do diagnóstico e da análise. Os desenvolvedores que estão instrumentando aplicativos podem decidir qual tipo de métrica é mais adequado a um cenário específico, dependendo do tamanho do aplicativo, do volume esperado de telemetria e dos requisitos de negócios para precisão e alertas de métricas.

## <a name="log-based-metrics"></a>Métricas baseadas em log

Até recentemente, o modelo de dados de telemetria de monitoramento de aplicativos no Application Insights era exclusivamente baseado em um pequeno número de tipos predefinidos de eventos, como solicitações, exceções, chamadas de dependência, exibições de página etc. Os desenvolvedores podem usar o SDK para emitir esses eventos manualmente (escrevendo código que invoca explicitamente o SDK) ou podem contar com a coleção automática de eventos da instrumentação automática. Em ambos os casos, o back-end de Application Insights armazena todos os eventos coletados como logs, e as folhas de Application Insights no portal do Azure atuam como uma ferramenta analítica e de diagnóstico para visualizar dados baseados em eventos de logs.

O uso de logs para manter um conjunto completo de eventos pode trazer um ótimo valor analítico e de diagnóstico. Por exemplo, você pode obter uma contagem exata de solicitações para uma URL específica com o número de usuários distintos que fizeram essas chamadas. Ou você pode obter rastreamentos de diagnóstico detalhados, incluindo exceções e chamadas de dependência para qualquer sessão de usuário. Ter esse tipo de informação pode melhorar significativamente a visibilidade da integridade e do uso do aplicativo, permitindo reduzir o tempo necessário para diagnosticar problemas com um aplicativo. 

Ao mesmo tempo, coletar um conjunto completo de eventos pode ser impraticável (ou até mesmo impossível) para aplicativos que geram muita telemetria. Para situações em que o volume de eventos é muito alto, Application Insights implementa várias técnicas de redução de volume de telemetria, como [amostragem](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) e [filtragem](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) que reduzem o número de eventos coletados e armazenados. Infelizmente, reduzir o número de eventos armazenados também reduz a precisão das métricas que, em segundo plano, deve executar agregações de tempo de consulta dos eventos armazenados nos logs.

> [!NOTE]
> No Application Insights, as métricas baseadas na agregação de tempo de consulta de eventos e medidas armazenadas em logs são chamadas de métricas baseadas em log. Essas métricas normalmente têm muitas dimensões das propriedades do evento, o que as torna superiores para análise, mas a precisão dessas métricas é afetada negativamente por amostragem e filtragem.

## <a name="pre-aggregated-metrics"></a>Métricas previamente agregadas

Além das métricas baseadas em log, no Outono de 2018, a equipe de Application Insights enviou uma visualização pública das métricas que são armazenadas em um repositório especializado que é otimizado para a série temporal. As novas métricas não são mais mantidas como eventos individuais com muitas propriedades. Em vez disso, eles são armazenados como uma série temporal de agregação e somente com dimensões de chave. Isso torna as novas métricas superiores no momento da consulta: a recuperação de dados ocorre muito mais rápido e requer menos capacidade de computação. Isso, portanto, permite novos cenários, como [alertas quase em tempo real sobre dimensões de métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), [painéis](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)mais responsivos e muito mais.

> [!IMPORTANT]
> Ambas as métricas baseadas em log e previamente agregadas coexistem no Application Insights. Para diferenciar os dois, no Application Insights UX, as métricas previamente agregadas agora são chamadas de "métricas padrão (versão prévia)", enquanto as métricas tradicionais dos eventos foram renomeadas para "métricas baseadas em log".

Os SDKs mais recentes ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) ou posterior para .net) métricas pré-configuradas durante a coleta antes do início das técnicas de redução de volume de telemetria. Isso significa que a precisão das novas métricas não é afetada por amostragem e filtragem ao usar os SDKs de Application Insights mais recentes.

Para os SDKs que não implementam a pré-autenticação (ou seja, versões mais antigas do Application Insights SDKs ou instrumentação de navegador), o back-end Application Insights ainda preenche as novas métricas agregando os eventos recebidos pelo aplicativo Ponto de extremidade de coleta de eventos de informações. Isso significa que, embora você não se beneficie do volume reduzido de dados transmitidos pela rede, ainda poderá usar as métricas previamente agregadas e experimentar um melhor desempenho e suporte ao alerta dimensional quase em tempo real com SDKs que não métricas de agregação prévia durante a coleta.

Vale a pena mencionar que o ponto de extremidade de coleta agrega eventos antes da amostragem de ingestão, o que significa que a [amostragem de ingestão](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) nunca afetará a precisão das métricas previamente agregadas, independentemente da versão do SDK que você usa com seu aplicativo.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Usando a pré-agregação com Application Insights métricas personalizadas

Você pode usar a pré-autenticação com métricas personalizadas. Os dois principais benefícios são a capacidade de configurar e alertar em uma dimensão de uma métrica personalizada e reduzir o volume de dados enviados do SDK para o ponto de extremidade da coleção de Application Insights.

Há várias [maneiras de enviar métricas personalizadas do SDK do Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Se sua versão do SDK oferece os métodos [getmetric e trackvalue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) , essa é a maneira preferida de enviar métricas personalizadas, já que, nesse caso, a pré-autenticação ocorre dentro do SDK, não apenas reduzindo o volume de dados armazenados no Azure, mas também o volume de dados transmitidos do sdk para Application insights. Caso contrário, use o método [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) , que irá agregar previamente os eventos de métrica durante a ingestão de dados.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensões de métricas personalizadas e pré-agregação

Todas as métricas que você envia usando chamadas de API [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) ou [Getmetric e trackvalue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) são armazenadas automaticamente nos repositórios e nos armazenamentos de métricas. No entanto, embora a versão baseada em log da métrica personalizada sempre retenha todas as dimensões, a versão previamente agregada da métrica é armazenada por padrão sem dimensões. Você pode ativar a coleta de dimensões de métricas personalizadas na guia [uso e custo estimado](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) marcando "Habilitar alertas em dimensões de métricas personalizadas": 

![Uso e custo estimado](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Por que a coleta de dimensões de métricas personalizadas é desativada por padrão?

A coleção de dimensões de métricas personalizadas é desativada por padrão porque, no futuro, o armazenamento de métricas personalizadas com dimensões será cobrado separadamente da Application Insights, enquanto o armazenamento das métricas personalizadas não dimensionais permanecerá livre (até uma cota) . Você pode aprender sobre as futuras alterações de modelo de preços em nossa [página de preços](https://azure.microsoft.com/pricing/details/monitor/)oficial.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Criando gráficos e explorando métricas agregadas padrão e baseadas em log

Use [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) para plotar gráficos de métricas previamente agregadas e baseadas em log e para criar painéis com gráficos. Depois de selecionar o recurso de Application Insights desejado, use o seletor de namespace para alternar entre as métricas padrão (visualização) e baseada em log ou selecione um namespace de métrica personalizado:

![Espaço de nomes de métricas](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Passos seguintes

* [Alertas quase em tempo real](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [Getmetric e Trackvalue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)
