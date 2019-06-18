---
title: As métricas com base em log e pré-agregados no Application Insights do Azure | Documentos da Microsoft
description: Por que usar baseados em registo versus métricas agregadas previamente no Azure Application Insights
services: application-insights
keywords: ''
author: vgorbenko
ms.author: vitalyg
ms.reviewer: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9520cbb9973071bf1c52266d7718837607c1d10f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66256136"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Métricas com base em log e pré-agregados no Application Insights

Este artigo explica a diferença entre "tradicionais" métricas do Application Insights que se baseiam em registos e métricas previamente agregadas que estão atualmente em pré-visualização pública. Ambos os tipos de métricas estão disponíveis para os utilizadores do Application Insights e cada traz um valor exclusivo na monitorização de estado de funcionamento do aplicativo, diagnóstico e análise. Os desenvolvedores que são instrumentar aplicações podem decidir que tipo de métrica é melhor adequado para um cenário específico, dependendo do tamanho do aplicativo, volume de telemetria e requisitos comerciais esperado para precisão de métricas e alertas.

## <a name="log-based-metrics"></a>Métricas baseadas no registo

Até recentemente, a aplicação a monitorizar o modelo de dados de telemetria no Application Insights foi exclusivamente com base num pequeno número de tipos predefinidos de eventos, tais como pedidos, exceções, chamadas de dependência, vistas de página, etc. Os programadores podem utilizar o SDK para optar por emitir estes eventos manualmente (ao escrever código que invoca explicitamente o SDK) ou eles podem contar a coleção automática de eventos de instrumentação automática. Em ambos os casos, o back-end do Application Insights armazena todos os eventos recolhidos como registos e os painéis do Application Insights no portal do Azure funcionam como uma ferramenta de análise e diagnóstico para visualizar dados com base em eventos de registos.

Utilizar registos para manter um conjunto completo de eventos, pode colocar um grande valor analítico e de diagnóstico. Por exemplo, pode obter uma contagem de exata de pedidos para uma determinada URL com o número de utilizadores distintos que efetuou essas chamadas. Ou pode obter rastreios de diagnóstico detalhados, incluindo as exceções e chamadas de dependência para todas as sessões de utilizador. Ter este tipo de informações pode melhorar significativamente a visibilidade sobre o estado de funcionamento do aplicativo e a utilização, que permite a fim de reduzir o tempo necessário para diagnosticar problemas com uma aplicação. 

Ao mesmo tempo, recolha um conjunto completo de eventos pode ser impraticável (ou até mesmo impossível) para aplicações que geram uma grande quantidade de telemetria. Para situações quando o volume de eventos é demasiado elevado, Application Insights implementa várias técnicas de redução telemetria de volume, tal como [amostragem](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) e [filtragem](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) que reduzir o número de eventos recolhidos e armazenados. Infelizmente, reduzir o número de eventos armazenados também reduz a precisão das métricas de que, nos bastidores, tem de efetuar agregações de tempo de consulta dos eventos armazenados nos registos.

> [!NOTE]
> No Application Insights, as métricas que são baseiam-se a agregação de tempo de consulta de eventos e medidas armazenadas nos registos são chamadas de métricas baseadas no registo. Estas métricas, normalmente, têm várias dimensões de propriedades do evento, o que as torna superior para análise, mas a precisão destas métricas é afetada negativamente por amostragem e filtragem.

## <a name="pre-aggregated-metrics"></a>Métricas agregadas previamente

Além das métricas baseadas no registo, no Outono de 2018, a equipe do Application Insights foi fornecido uma pré-visualização pública de métricas que estão armazenadas num repositório especializado otimizada para a série de tempo. A nova métrica já não é mantida como eventos individuais com muitas das propriedades. Em vez disso, eles são armazenados como uma série de tempo pré-agregados e apenas com dimensões de chave. Isso torna a nova métrica superior no momento da consulta: obter dados acontece muito mais rápido e requer menos poder de computação. Isso permite conseqüentemente novos cenários de tal como [perto de alertas em tempo real em dimensões de métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)e mais dinâmica [dashboards](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)e muito mais.

> [!IMPORTANT]
> Métricas, com base em log tanto pré-agregados coexistam no Application Insights. Para diferenciar os dois, no Application Insights UX as métricas pré-agregados são agora denominadas "Métricas Standard (pré-visualização)", enquanto as métricas tradicionais dos eventos foram renomeadas para "métricas baseadas no registo".

Os SDKs mais recente ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK ou posterior para .NET) surgir métricas de agregação previamente durante a recolha de antes de técnicas de redução de volume de telemetria. Isso significa que a precisão das métricas de novo não é afetada por amostragem e filtros ao utilizar os SDKs do Application Insights mais recente.

Para os SDKs que não implementam pré-agregação (ou seja, as versões mais antigas de SDKs do Application Insights ou de instrumentação do navegador) o back-end do Application Insights preenche ainda novas métricas, agregar os eventos recebidos pela aplicação Ponto final de recolha do insights eventos. Isso significa que embora não se beneficiam do volume reduzido de transmitidos pela conexão de dados, pode ainda utilizar as métricas pré-agregados e experiência um melhor desempenho e o suporte de quase em tempo real dimensional alertas com os SDKs que não métricas de agregação previamente durante a recolha.

Vale a pena mencionar que o ponto final de recolha previamente agrega eventos antes de amostragem de ingestão, o que significa que [amostragem de ingestão](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) nunca terá impacto a precisão das métricas pré-agregados, independentemente da versão do SDK Utilize com a sua aplicação.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Utilizar pré-agregação com métricas personalizadas do Application Insights

Pode usar pré-agregação com métricas personalizadas. Os dois principais benefícios são a capacidade de configurar e alertar relativamente uma dimensão de uma métrica personalizada e reduzindo o volume de dados enviados do SDK para o ponto de final de recolha do Application Insights.

Existem vários [formas de envio de métricas personalizadas a partir do Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Se a sua versão do SDK oferece a [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) métodos, essa é a maneira preferencial de envio de métricas personalizadas, uma vez que, neste caso pré-agregação acontece dentro do SDK, não apenas reduzir o volume de dados armazenados no O Azure, mas também o volume de dados transmitidos do SDK para o Application Insights. Caso contrário, utilize o [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) método, que será pré-agregar métrica eventos durante a ingestão de dados.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensões de métricas personalizadas e pré-agregação

Todas as métricas que envia usando [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) ou [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) chamadas à API são automaticamente armazenados nos arquivos de métricas e registos. No entanto, enquanto a versão baseada no registo de sua métrica personalizada sempre mantém todas as dimensões, a versão pré-agregados da métrica é armazenada por padrão com nenhuma dimensão. Pode ativar a coleção de dimensões de métricas personalizadas a [utilização e custo estimado](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) separador ao selecionar "Ativar alertas em dimensões de métricas personalizadas": 

![Utilização e custo estimado](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Por que é a coleção de dimensões de métricas personalizadas desativada por predefinição?

A coleção de dimensões de métricas personalizadas é desativada por padrão porque no futuro armazenar métricas personalizadas com as dimensões é cobrada em separado do Application Insights, enquanto a armazenar as métricas personalizadas não dimensionais serão gratuitas (até uma quota) . Pode aprender sobre as alterações de modelo de preços futuras no nosso oficial [página de preços](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Criação de gráficos e explorar métricas pré-agregados baseados em registo e standard

Uso [Explorador de métricas do Azure Monitor](../platform/metrics-getting-started.md) para desenhar gráficos de métricas pré-agregados e baseados em registo e para dashboards de autor com gráficos. Depois de selecionar o recurso do Application Insights pretendido, utilize o Seletor de espaço de nomes para alternar entre o standard (pré-visualização) e as métricas baseadas no registo ou selecione um espaço de nomes de métrica personalizado:

![Espaço de nomes de métrica](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Passos Seguintes

* [Quase em tempo real de alertas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)