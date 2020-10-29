---
title: Métricas baseadas em registos e pré-agregadas em Azure Application Insights Microsoft Docs
description: Porquê utilizar métricas baseadas em registos versus pré-agregadas em Insights de Aplicação Azure
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9b93ac774dffb837d93853353e83b8da4ab4d8d4
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027164"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Métricas baseadas no registo e pré-agregadas no Application Insights

Este artigo explica a diferença entre as métricas "tradicionais" de Insights de Aplicação que são baseadas em registos e métricas pré-agregadas. Ambos os tipos de métricas estão disponíveis para os utilizadores de Application Insights, e cada um traz um valor único na monitorização da saúde, diagnóstico e análise de aplicações. Os desenvolvedores que estão a instrumentar aplicações podem decidir qual o tipo de métrica mais adequado a um determinado cenário, dependendo do tamanho da aplicação, volume esperado de telemetria, e requisitos de negócio para precisão e alerta de métricas.

## <a name="log-based-metrics"></a>Métricas baseadas em registos

No passado, o modelo de dados de telemetria de monitorização da aplicação em Application Insights baseava-se unicamente num pequeno número de tipos de eventos predefinidos, tais como pedidos, exceções, chamadas de dependência, visualizações de páginas, etc. Os desenvolvedores podem usar o SDK para emitir estes eventos manualmente (escrevendo código que invoca explicitamente o SDK) ou podem confiar na recolha automática de eventos a partir de auto-instrumentação. Em qualquer dos casos, o backend Application Insights armazena todos os eventos recolhidos como registos, e as lâminas application Insights no portal Azure funcionam como uma ferramenta analítica e de diagnóstico para visualizar dados baseados em eventos a partir de registos.

Usar registos para reter um conjunto completo de eventos pode trazer um grande valor analítico e de diagnóstico. Por exemplo, você pode obter uma contagem exata de pedidos para um URL particular com o número de utilizadores distintos que fizeram estas chamadas. Ou pode obter vestígios de diagnóstico detalhados, incluindo exceções e pedidos de dependência para qualquer sessão de utilizador. Ter este tipo de informação pode melhorar significativamente a visibilidade na saúde e utilização da aplicação, permitindo reduzir o tempo necessário para diagnosticar problemas com uma app.

Ao mesmo tempo, recolher um conjunto completo de eventos pode ser impraticável (ou mesmo impossível) para aplicações que gerem um grande volume de telemetria. Para situações em que o volume de eventos é demasiado elevado, a Application Insights implementa várias técnicas de redução do volume de telemetria, tais como [amostragem](./sampling.md) e filtragem que [reduzem](./api-filtering-sampling.md) o número de eventos recolhidos e armazenados. Infelizmente, a redução do número de eventos armazenados também diminui a precisão das métricas que, nos bastidores, devem realizar agregações de tempo de consulta dos eventos armazenados em troncos.

> [!NOTE]
> Em Application Insights, as métricas que se baseiam na agregação de tempo de consulta de eventos e medições armazenadas em registos são chamadas métricas baseadas em log. Estas métricas normalmente têm muitas dimensões das propriedades do evento, o que as torna superiores para análise, mas a precisão destas métricas é negativamente afetada pela amostragem e filtragem.

## <a name="pre-aggregated-metrics"></a>Métricas pré-agregadas

Além das métricas baseadas em registos, no final de 2018, a equipa da Application Insights enviou uma pré-visualização pública de métricas que são armazenadas num repositório especializado que está otimizado para séries temporais. As novas métricas já não são mantidas como eventos individuais com muitas propriedades. Em vez disso, são armazenados como séries temporâmia pré-agregadas, e apenas com dimensões-chave. Isto torna as novas métricas superiores no tempo de consulta: recuperar dados acontece muito mais rápido e requer menos poder de computação. Isto permite, consequentemente, novos cenários, como [o alerta quase em tempo real sobre as dimensões das métricas](../platform/alerts-metric-near-real-time.md), dashboards mais [responsivos](./overview-dashboard.md)e muito mais.

> [!IMPORTANT]
> Ambas as métricas baseadas em log e pré-agregadas coexistem em Insights de Aplicação. Para diferenciar os dois, no Application Insights UX as métricas pré-agregadas são agora chamadas de "métricas padrão (pré-visualização)", enquanto as métricas tradicionais dos eventos foram renomeadas para "métricas baseadas em log".

Os novos SDKs[(Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK ou mais tarde para .NET) métricas pré-agregadas durante a recolha. Isto aplica-se às  [métricas padrão enviadas por padrão para](../platform/metrics-supported.md#microsoftinsightscomponents) que a precisão não seja afetada pela amostragem ou filtragem. Aplica-se também às métricas personalizadas enviadas através da [GetMetric,](./api-custom-events-metrics.md#getmetric) resultando em menos ingestão de dados e menor custo.

Para os SDKs que não implementam pré-agregação (isto é, versões mais antigas de SDKs application insights ou para instrumentação de navegador) o backend Application Insights ainda povoa as novas métricas agregando os eventos recebidos pelo ponto final de recolha de eventos Application Insights. Isto significa que, embora não beneficie do volume reduzido de dados transmitidos através do fio, ainda pode usar as métricas pré-agregadas e experimentar um melhor desempenho e suporte do alerta dimensional próximo em tempo real com SDKs que não pré-agregam métricas durante a recolha.

Vale a pena referir que o ponto final de recolha pré-agrega eventos antes da amostragem de ingestão, o que significa que a amostragem de [ingestão](./sampling.md) nunca terá impacto na precisão das métricas pré-agregadas, independentemente da versão SDK que utiliza com a sua aplicação.  

### <a name="sdk-supported-pre-aggregated-metrics-table"></a>Tabela de métricas pré-agregadas suportadas pela SDK

| SDKs de produção corrente | Métricas Padrão (Pré Agregação SDK) | Métricas personalizadas (sem pré-agregação SDK) | Métricas Personalizadas (com Pré Agregação SDK)|
|------------------------------|-----------------------------------|----------------------------------------------|---------------------------------------|
| .NET Core e .NET Framework | Suportado (V2.13.1+)| Suportado via [TrackMetric](api-custom-events-metrics.md#trackmetric)| Suportado (V2.7.2+) via [GetMetric](get-metric.md) |
| Java                         | Não suportado       | Suportado via [TrackMetric](api-custom-events-metrics.md#trackmetric)| Não suportado                           |
| Node.js                      | Não suportado       | Suportado via  [TrackMetric](api-custom-events-metrics.md#trackmetric)| Não suportado                           |
| Python                       | Não suportado       | Suportado                                 | Suportado via [OpenCensus.stats](opencensus-python.md#metrics) |  


### <a name="codeless-supported-pre-aggregated-metrics-table"></a>Tabela de métricas pré-agregadas suportadas sem codeless

| SDKs de produção corrente | Métricas Padrão (Pré Agregação SDK) | Métricas personalizadas (sem pré-agregação SDK) | Métricas Personalizadas (com Pré Agregação SDK)|
|-------------------------|--------------------------|-------------------------------------------|-----------------------------------------|
| ASP.NET                 | Apoiado <sup> 1<sup>    | Não suportado                             | Não suportado                           |
| ASP.NET Core            | Apoiado <sup> 2<sup>    | Não suportado                             | Não suportado                           |
| Java                    | Não suportado            | Não suportado                             | [Suportado](java-in-process-agent.md#metrics) |
| Node.js                 | Não suportado            | Não suportado                             | Não suportado                           |

1. ASP.NET anexação codificada no Serviço de Aplicações apenas emite métricas em modo de monitorização "completo". ASP.NET anexação sem código no Serviço de Aplicações, VM/VMSS e No-Premir emite métricas padrão sem dimensões. SDK é necessário para todas as dimensões.
2. ASP.NET Ligação codificada do Núcleo no Serviço de Aplicações emite métricas padrão sem dimensões. SDK é necessário para todas as dimensões.

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Utilização de pré-agregação com métricas personalizadas do Application Insights

Pode utilizar pré-agregação com métricas personalizadas. Os dois principais benefícios são a capacidade de configurar e alertar sobre uma dimensão de uma métrica personalizada e reduzir o volume de dados enviados do SDK para o ponto final de recolha de Insights de Aplicação.

Existem várias [formas de enviar métricas personalizadas do Application Insights SDK](./api-custom-events-metrics.md). Se a sua versão do SDK oferece os métodos [GetMetric e TrackValue,](./api-custom-events-metrics.md#getmetric) esta é a forma preferida de enviar métricas personalizadas, uma vez que neste caso a pré-agregação acontece dentro do SDK, não só reduzindo o volume de dados armazenados em Azure, mas também o volume de dados transmitidos do SDK para a Application Insights. Caso contrário, utilize o método [trackMetric,](./api-custom-events-metrics.md#trackmetric)  que irá pré-agregar eventos métricos durante a ingestão de dados.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensões de métricas personalizadas e pré-agregação

Todas as métricas que envia usando as chamadas [API de trackMetric](./api-custom-events-metrics.md#trackmetric) ou [GetMetric e TrackValue](./api-custom-events-metrics.md#getmetric) são automaticamente armazenadas em lojas de registos e métricas. No entanto, embora a versão baseada em log da sua métrica personalizada mantenha sempre todas as dimensões, a versão pré-agregada da métrica é armazenada por padrão sem dimensões. Pode ativar a recolha de dimensões de métricas personalizadas no separador [de custos e estimado,](./pricing.md) verificando "Ativar as dimensões métricas personalizadas": 

![Utilização e custo estimado](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Por que a recolha de dimensões de métricas personalizadas é desligada por padrão?

A recolha de dimensões de métricas personalizadas é desapaixada por padrão porque no futuro armazenar métricas personalizadas com dimensões será faturada separadamente da Application Insights, enquanto o armazenamento das métricas personalizadas não dimensionais permanecerá livre (até uma quota). Pode conhecer as próximas alterações do modelo de preços na nossa página oficial [de preços.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Criação de gráficos e exploração de métricas pré-agregadas baseadas em log e padrão

Use [o Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) para traçar gráficos a partir de métricas pré-agregadas e baseadas em log, e para autorize dashboards com gráficos. Depois de selecionar o recurso de Insights de Aplicação pretendido, utilize o seletor de espaço de nome para alternar entre métricas padrão (pré-visualização) e baseadas em log, ou selecione um espaço de nome métrico personalizado:

![Espaço de nome métrico](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Modelos de preços para métricas de Insights de Aplicações

Ingerir métricas em Application Insights, quer baseadas em registos ou pré-agregadas, gerará custos com base no tamanho dos dados ingeridos, como descrito [aqui.](./pricing.md#pricing-model) As suas métricas personalizadas, incluindo todas as suas dimensões, estão sempre armazenadas na loja de registos Application Insights; além disso, uma versão pré-agregada das suas métricas personalizadas (sem dimensões) é reencaminhada para a loja de métricas por padrão.

Selecionar o Enable alerting sobre a opção [de dimensões métricas personalizadas](#custom-metrics-dimensions-and-pre-aggregation) para armazenar todas as dimensões das métricas pré-agregadas na loja métrica, pode gerar custos **adicionais** com base nos [preços das Métricas Personalizadas](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Passos seguintes

* [Alerta em tempo real](../platform/alerts-metric-near-real-time.md)
* [GetMetric e TrackValue](./api-custom-events-metrics.md#getmetric)
