---
title: O Assessor de Métricas frequentemente fez perguntas
titleSuffix: Azure Cognitive Services
description: Perguntas frequentes sobre o serviço Metrics Advisor.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 0fde9a0f46073a2f3a24962ea58431581455f474
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939047"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>O Assessor de Métricas frequentemente fez perguntas

### <a name="what-is-the-cost-of-my-instance"></a>Qual é o custo do meu caso?

Atualmente não há um custo para usar o seu exemplo durante a pré-visualização.

### <a name="why-is-the-demo-website-readonly"></a>Porque é que o site de demonstração é lido apenas?

O [site de demonstração](https://anomaly-detector.azurewebsites.net/) está disponível publicamente. Este caso é feito apenas para evitar o upload acidental de quaisquer dados.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Por que não posso criar o recurso? O "nível de preços" não está disponível e diz "Já criou 1 S0 para esta subscrição"?

:::image type="content" source="media/pricing.png" alt-text="Mensagem quando um recurso F0 já existe":::

Durante a pré-visualização pública, apenas um exemplo de Metrics Advisor é permitido criar sob uma subscrição, numa região.

Se já tiver um caso criado na mesma região usando a mesma subscrição, pode tentar uma região diferente ou uma subscrição diferente para criar um novo exemplo. Também pode eliminar um caso existente para criar um novo.

Se já eliminou a instância existente, mas ainda vê o erro, aguarde cerca de 20 minutos após a eliminação do recurso antes de criar uma nova instância.

## <a name="basic-concepts"></a>Conceitos básicos

### <a name="what-is-multi-dimensional-time-series-data"></a>O que são dados multidimensionais da série temporal?

Veja a definição [métrica multidimensional](glossary.md#multi-dimensional-metric)  no glossário.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Quantos dados são necessários para o Metrics Advisor iniciar a deteção de anomalias?

No mínimo, um ponto de dados pode desencadear a deteção de anomalias. No entanto, isto não traz a melhor precisão. O serviço assumirá uma janela de pontos de dados anteriores usando o valor especificado como a regra "preencher lacuna" durante a criação do feed de dados.

Recomendamos ter alguns dados antes do tempo que pretende ser detetado.
Com base na granularidade dos seus dados, o valor dos dados recomendado varia como abaixo.

| Granularidade | Valor de dados recomendado para deteção |
| ----------- | ------------------------------------- |
| Menos de 5 minutos | 4 dias de dados |
| 5 minutos para 1 dia | 28 dias de dados |
| Mais de 1 dia, a 31 dias | 4 anos de dados |
| Maior que 31 dias | 48 anos de dados |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Por que o Metrics Advisor não deteta anomalias a partir de dados históricos?

O Metrics Advisor foi concebido para detetar dados de streaming ao vivo. Há uma limitação do comprimento máximo de dados históricos que o serviço vai olhar para trás e executar deteção de anomalias. Significa que apenas os pontos de dados após uma certa primeira hora de tempo terão resultados de deteção de anomalias. A primeira marca de tempo depende da granularidade dos seus dados.

Com base na granularidade dos seus dados, os comprimentos dos dados históricos que terão resultados de deteção de anomalias são os seguintes.

| Granularidade | Comprimento máximo dos dados históricos para deteção de anomalias |
| ----------- | ------------------------------------- |
| Menos de 5 minutos | Tempo de bordo - 13 horas |
| 5 minutos a menos de 1 hora | Tempo de bordo - 4 dias  |
| 1 hora a menos de 1 dia | Tempo de bordo - 14 dias  |
| 1 dia | Tempo de bordo - 28 dias  |
| Maior que 1 dia, menos de 31 dias | Tempo de bordo - 2 anos  |
| Maior que 31 dias | Tempo de bordo - 24 anos   |

### <a name="more-concepts-and-technical-terms"></a>Mais conceitos e termos técnicos

Por favor, vá ao [Glossário](glossary.md) ler mais.

## <a name="how-do-i-detect-such-kinds-of-anomalies"></a>Como deteto este tipo de anomalias? 

### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Como deteto picos & diminui como anomalias?

Se tiver limiares rígidos predefinidos, pode definir manualmente "limiar rígido" em [configurações de deteção de anomalias](how-tos/configure-metrics.md#anomaly-detection-methods).
Se não houver limiares, pode usar "deteção inteligente" que é alimentada por IA. Consulte para [sintonizar a configuração de deteção](how-tos/configure-metrics.md#tune-the-detecting-configuration) para obter detalhes.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Como deteto a inconformidade com padrões regulares (sazonais) como anomalias?

A "deteção inteligente" é capaz de aprender o padrão dos seus dados, incluindo padrões sazonais. Em seguida, deteta os pontos de dados que não se conformam com os padrões regulares como anomalias. Consulte para [sintonizar a configuração de deteção](how-tos/configure-metrics.md#tune-the-detecting-configuration) para obter detalhes.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Como deteto linhas planas como anomalias?

Se os seus dados são normalmente bastante instáveis e oscilam muito, e pretende ser alertado quando se torna demasiado estável ou até se torna uma linha plana, "Alterar limiar" é capaz de ser configurado para detetar tais pontos de dados quando a mudança é muito pequena.
Consulte [as configurações de deteção de anomalias](how-tos/configure-metrics.md#anomaly-detection-methods) para obter mais detalhes.

## <a name="next-steps"></a>Passos Seguintes
- [Métricas Conselheiro Visão Geral](overview.md)
- [Experimente o site de demonstração](quickstarts/explore-demo.md)
- [Utilizar o portal Web](quickstarts/web-portal.md)