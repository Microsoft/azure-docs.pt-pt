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
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: e4a75bdd6147ee2189660c37062c5bec9d55d512
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631749"
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

Consulte também o [Glossário](glossary.md) para mais informações.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Como escrevo uma consulta válida para ingerir os meus dados?  

Para o Metrics Advisor ingerir os seus dados, terá de criar uma consulta que retorne as dimensões dos seus dados num único timetamp. O advisor de métricas executará esta consulta várias vezes para obter os dados de cada timetamp. 

Note que a consulta deve voltar no máximo um registo para cada combinação de dimensão, a uma determinada hora de tempo. Todos os registos devolvidos devem ter a mesma hora. Não deve haver registos duplicados devolvidos pela consulta.

Por exemplo, suponha que crie a consulta abaixo, para uma métrica diária: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Certifique-se de que utiliza a granularidade correta para as suas séries hors de trabalho. Para uma métrica de hora a hora, usaria: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Note que estas consultas apenas devolvem dados a uma única hora de tempo, e contêm todas as combinações de dimensão a serem ingeridas pelo Metrics Advisor. 

:::image type="content" source="media/query-result.png" alt-text="Mensagem quando um recurso F0 já existe" lightbox="media/query-result.png":::


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