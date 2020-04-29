---
title: Métricas baseadas em log e pré-agregados em Insights de Aplicação Azure [ Microsoft Docs
description: Porquê utilizar métricas baseadas em log versus pré-agregados em Insights de Aplicação Azure
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 30487eebed361e5b010df023a9b1a44f96590b14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81271085"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Métricas baseadas no registo e pré-agregadas no Application Insights

Este artigo explica a diferença entre métricas "tradicionais" de Insights de Aplicação que são baseadas em registos, e métricas pré-agregadas que estão atualmente em pré-visualização pública. Ambos os tipos de métricas estão disponíveis para os utilizadores de Insights de Aplicação, e cada um traz um valor único na monitorização da saúde da aplicação, diagnósticos e análise. Os desenvolvedores que estão a instrumentar aplicações podem decidir qual o tipo de métrica mais adequado a um determinado cenário, dependendo da dimensão da aplicação, volume esperado de telemetria e requisitos de negócio para a precisão e alerta de métricas.

## <a name="log-based-metrics"></a>Métricas baseadas em log

Até recentemente, o modelo de dados de telemetria de monitorização da aplicação em Application Insights baseava-se exclusivamente num pequeno número de tipos de eventos pré-definidos, tais como pedidos, exceções, chamadas de dependência, visualizações de páginas, etc. Os desenvolvedores podem usar o SDK para emitir estes eventos manualmente (escrevendo código que invoca explicitamente o SDK) ou podem confiar na recolha automática de eventos a partir da instrumentação automática. Em qualquer dos casos, o backend da Application Insights armazena todos os eventos recolhidos como registos, e as lâminas de Insights de Aplicação no portal Azure funcionam como uma ferramenta analítica e de diagnóstico para visualizar dados baseados em eventos a partir de registos.

A utilização de registos para reter um conjunto completo de eventos pode trazer um grande valor analítico e de diagnóstico. Por exemplo, pode obter uma contagem exata de pedidos para um URL específico com o número de utilizadores distintos que fizeram estas chamadas. Ou pode obter vestígios de diagnóstico detalhados, incluindo exceções e chamadas de dependência para qualquer sessão de utilizador. Ter este tipo de informação pode melhorar significativamente a visibilidade na saúde e utilização da aplicação, permitindo reduzir o tempo necessário para diagnosticar problemas com uma app.

Ao mesmo tempo, a recolha de um conjunto completo de eventos pode ser impraticável (ou mesmo impossível) para aplicações que gerem um grande volume de telemetria. Para situações em que o volume de eventos é demasiado elevado, a Application Insights implementa várias técnicas de redução de volume de telemetria, tais como [amostragem](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) e [filtragem](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) que reduzem o número de eventos recolhidos e armazenados. Infelizmente, a redução do número de eventos armazenados também diminui a precisão das métricas que, nos bastidores, devem realizar agregações de tempo de consulta dos eventos armazenados em registos.

> [!NOTE]
> Em Application Insights, as métricas que se baseiam na agregação do tempo de consulta de eventos e medições armazenadas em registos são chamadas métricas baseadas em log. Estas métricas têm tipicamente muitas dimensões das propriedades do evento, o que as torna superiores à análise, mas a precisão destas métricas é negativamente afetada pela amostragem e filtragem.

## <a name="pre-aggregated-metrics"></a>Métricas pré-agregadas

Além das métricas baseadas em log, no final de 2018, a equipa de Application Insights enviou uma pré-visualização pública de métricas que são armazenadas num repositório especializado otimizado para séries temporais. As novas métricas já não são mantidas como eventos individuais com muitas propriedades. Em vez disso, são armazenados como séries temporais pré-agregadas, e apenas com dimensões-chave. Isto torna as novas métricas superiores no momento da consulta: recuperar dados acontece muito mais rapidamente e requer menos potência computacional. Isto permite, consequentemente, novos cenários, como alertar quase em [tempo real sobre dimensões de métricas,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)painéis mais responsivos e muito mais. [dashboards](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)

> [!IMPORTANT]
> Ambas, métricas baseadas em log e pré-agregadas coexistem em Insights de Aplicação. Para diferenciar os dois, na Aplicação Insights UX as métricas pré-agregadas são agora chamadas de "Métricas Padrão (pré-visualização)", enquanto as métricas tradicionais dos eventos foram rebatizadas para "Métricas baseadas em Log".

Os novos SDKs ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK ou posteriormente para .NET) métricas pré-agregadas durante a recolha antes de iniciarem as técnicas de redução de volume de telemetria. Isto significa que a precisão das novas métricas não é afetada pela amostragem e filtragem ao utilizar os mais recentes SDKs de Insights de Aplicação.

Para os SDKs que não implementam a pré-agregação (isto é, versões mais antigas de SDKs de Insights de Aplicação ou para instrumentação de navegador), o backend da Aplicação Insights ainda povoa as novas métricas agregando os eventos recebidos pelo ponto final da coleção de eventos Application Insights. Isto significa que, embora não beneficie do reduzido volume de dados transmitidos através do fio, ainda pode utilizar as métricas pré-agregadas e experimentar um melhor desempenho e suporte do alerta dimensional quase em tempo real com SDKs que não pré-agregam métricas durante a recolha.

Vale referir que a recolha agrega eventos antes da amostragem de [ingestão,](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) o que significa que a amostragem de ingestão nunca terá impacto na precisão das métricas pré-agregadas, independentemente da versão SDK que utilize com a sua aplicação.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Usando a pré-agregação com métricas personalizadas de Application Insights

Pode utilizar a pré-agregação com métricas personalizadas. Os dois principais benefícios são a capacidade de configurar e alertar sobre uma dimensão de uma métrica personalizada e reduzir o volume de dados enviados do SDK para o ponto final de recolha de Insights de Aplicação.

Existem várias [formas de enviar métricas personalizadas a partir do Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Se a sua versão do SDK oferece os métodos [GetMetric e TrackValue,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) esta é a forma preferida de enviar métricas personalizadas, uma vez que neste caso a pré-agregação ocorre dentro do SDK, não só reduzindo o volume de dados armazenados no Azure, mas também o volume de dados transmitidos do SDK para Application Insights. Caso contrário, utilize o método [trackMetric,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) que pré-agregará eventos métricos durante a ingestão de dados.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensões de métricas personalizadas e pré-agregação

Todas as métricas que envia utilizando chamadas [TrackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) ou [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API são automaticamente armazenadas em lojas de registos e métricas. No entanto, embora a versão baseada em log da sua métrica personalizada mantenha sempre todas as dimensões, a versão pré-agregada da métrica é armazenada por padrão sem dimensões. Pode ativar a recolha de dimensões de métricas personalizadas no uso e no separador de [custos estimado,](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) verificando "Ativar o alerta sobre dimensões métricas personalizadas": 

![Utilização e custo estimado](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Porque é que a recolha de dimensões de métricas personalizadas é desligada por defeito?

A recolha de dimensões métricas personalizadas é desligada por padrão porque no futuro armazenar métricas personalizadas com dimensões será faturada separadamente da Application Insights, enquanto o armazenamento das métricas personalizadas não dimensionais permanecerá livre (até uma quota). Pode conhecer as próximas alterações do modelo de preços na nossa página oficial de [preços.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Criar gráficos e explorar métricas pré-agregadas baseadas em loge e padrão

Utilize o [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) para traçar gráficos a partir de métricas pré-agregadas e baseadas em log, e para autorde dashboards com gráficos. Depois de selecionar o recurso Desativado application Insights, utilize o marcador de espaço de nome para alternar entre as métricas padrão (pré-visualização) e log-based, ou selecione um espaço de nomes métrico personalizado:

![Espaço de nomemétrico](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Modelos de preços para métricas de Insights de Aplicação

Ingerir métricas em Insights de Aplicação, quer sejam baseados em log ou pré-agregados, gerará custos com base no tamanho dos dados ingeridos, como [aqui](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model)descrito . As suas métricas personalizadas, incluindo todas as suas dimensões, são sempre armazenadas na loja de log-store Application Insights; adicionalmente, uma versão pré-agregada das suas métricas personalizadas (sem dimensões) é reencaminhada para a loja de métricas por padrão.

Selecionar o [Alerta Enable sobre dimensões métricas personalizadas](#custom-metrics-dimensions-and-pre-aggregation) opção de armazenar todas as dimensões das métricas pré-agregadas na loja métrica, pode gerar custos **adicionais** com base nos preços das [Métricas Personalizadas](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Passos seguintes

* [Alerta perto em tempo real](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)