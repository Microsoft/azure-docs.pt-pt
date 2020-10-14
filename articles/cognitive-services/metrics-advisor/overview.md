---
title: O que é o serviço Metrics Advisor?
titleSuffix: Azure Cognitive Services
description: O que é o Assistente de Métricas?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 858bf042593045059c988ae1b2796b063b33bb6e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047578"
---
# <a name="what-is-metrics-advisor-preview"></a>O que é Metrics Advisor (pré-visualização)? 

Metrics Advisor faz parte da Azure Cognitive Services que utiliza a AI realizar monitorização de dados e deteção de anomalias em dados da série de tempo. O serviço automatiza o processo de aplicação de modelos aos seus dados, e fornece um conjunto de espaço de trabalho baseado na Web para ingestão de dados, deteção de anomalias e diagnósticos - sem necessidade de conhecer machine learning. Utilizar o Advisor métrica para:

* Analisar dados multidimensionais de várias fontes de dados 
* Identificar e correlacionar anomalias
* Configure e afina o modelo de deteção de anomalias utilizado nos seus dados
* Diagnosticar anomalias e ajudar na análise da causa raiz. 

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Métricas Conselheiro Visão Geral":::

## <a name="connect-to-a-variety-of-data-sources"></a>Ligar-se a uma variedade de fontes de dados

O Metrics Advisor pode ligar-se e ingerir dados [métricos multidimensionais](how-tos/onboard-your-data.md) de muitas lojas de dados, incluindo: SQL Server, Azure Blob Storage, MongoDB e muito mais. 

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Deteção de anomalias fáceis de usar e personalizáveis

* O Metrics Advisor seleciona automaticamente o melhor modelo para os seus dados, sem precisar de saber qualquer aprendizagem automática. 
* Monitorize automaticamente todas as séries de tempo dentro [de métricas multidimensionais](glossary.md#multi-dimensional-metric).
* Utilize [a afinação de parâmetros](how-tos/configure-metrics.md) e [feedback interativo](how-tos/anomaly-feedback.md) para personalizar o modelo aplicado nos seus dados e futuros resultados de deteção de anomalias.


## <a name="real-time-alerts-through-multiple-channels"></a>Alertas em tempo real através de vários canais

Sempre que forem detetadas anomalias, o Metrics Advisor é capaz de enviar alertas em tempo real através de [vários canais](how-tos/alerts.md) utilizando ganchos, tais como: ganchos de e-mail, ganchos web e ganchos Azure DevOps. As regras de alerta flexíveis permitem personalizar quais os alertas enviados e para onde.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Insights de diagnóstico inteligentes analisando anomalias

Analise anomalias detetadas em métricas multidimensionais, e gere [insights de diagnóstico inteligentes,](how-tos/diagnose-incident.md) incluindo a causa mais provável de raiz, árvores de diagnóstico, perfuração métrica, e muito mais. Ao configurar [o gráfico Métricas,](how-tos/metrics-graph.md)a análise de métricas cruzadas pode ajudar-te a visualizar incidentes.


## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: depois de embarcar os seus dados, pode afinar a deteção de anomalias e criar configurações para se adaptar ao seu cenário.

1. [Crie um recurso Azure](../cognitive-services-apis-create-account.md) para o Metrics Advisor. 
2. Experimente o site de demonstração para ver um exemplo Métricas Exemplo Exemplo Exemplo Métricas Exemplo de exemplo exemplo exemplo de dados de amostrado com dados de amostra pré-configurados. 
3. Construa o seu primeiro monitor utilizando o portal web.
    1. Integrar dados
    2. Deteção de anomalias afinadas
    3. Subscrever os alertas
    4. Ver insights de diagnóstico
1. Utilize a API REST para personalizar a sua instância.

## <a name="next-steps"></a>Passos seguintes

* Experimente o [site de demonstração.](quickstarts/explore-demo.md)
* Explore um arranque rápido: [Monitorize a sua primeira métrica na web](quickstarts/web-portal.md).
* Explore um arranque rápido: [Utilize as APIs REST para personalizar a sua solução.](quickstarts/rest-api.md)
