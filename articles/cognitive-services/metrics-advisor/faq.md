---
title: O Assessor de Métricas frequentemente fez perguntas
titleSuffix: Azure Cognitive Services
description: Perguntas frequentes sobre o serviço Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: mbullwin
ms.openlocfilehash: da4dc3579630d641fcbc1d4321b56de0cc09d555
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893582"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>O Assessor de Métricas frequentemente fez perguntas

### <a name="what-is-the-cost-of-my-instance"></a>Qual é o custo do meu caso?

Atualmente não há um custo para usar o seu exemplo durante a pré-visualização.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Por que não posso criar o recurso? O "nível de preços" não está disponível e diz "Já criou 1 S0 para esta subscrição"?

:::image type="content" source="media/pricing.png" alt-text="Mensagem quando um recurso F0 já existe":::

Durante a pré-visualização pública, apenas um exemplo de Metrics Advisor pode ser criado por região sob uma subscrição.

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

## <a name="advanced-concepts"></a>Conceitos avançados

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Como é que o Metric Advisor constrói uma árvore de incidentes para métricas multidimensionais?

Uma métrica pode ser dividida em múltiplas séries de tempo por dimensões. Por exemplo, a métrica `Response latency` é monitorizada para todos os serviços pertencentes à equipa. A `Service` categoria poderia ser usada como uma dimensão para enriquecer a métrica, por isso somos `Response latency` divididos por , e assim por `Service1` `Service2` diante. Cada serviço poderia ser implantado em diferentes máquinas em vários centros de dados, para que a métrica pudesse ser ainda mais dividida por `Machine` e `Data center` .

|Serviço| Centro de dados| Máquina  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

Partindo do `Response latency` total, podemos perfurar a métrica `Service` por, `Data center` e `Machine` . No entanto, talvez faça mais sentido para os proprietários de serviços usarem o caminho , ou talvez faça mais sentido para os engenheiros de `Service`  ->  `Data center`  ->  `Machine` infraestruturas usarem o `Data Center`  ->  `Machine`  ->  `Service` caminho. Tudo depende dos requisitos de negócio individuais dos seus utilizadores. 

No Metric Advisor, os utilizadores podem especificar qualquer caminho que queiram perfurar ou rolar a partir de um nó da topologia hierárquica. Mais precisamente, a topologia hierárquica é um gráfico acíclico direcionado em vez de uma estrutura de árvore. Há uma topologia hierárquica completa que consiste em todas as combinações de dimensões potenciais, como esta: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="Mensagem quando um recurso F0 já existe" lightbox="media/dimension-combinations-view.png":::

Em teoria, se a dimensão `Service` tem `Ls` valores distintos, a dimensão `Data center` tem `Ldc` valores distintos, e a dimensão `Machine` tem `Lm` valores distintos, então pode haver `(Ls + 1) * (Ldc + 1) * (Lm + 1)` combinações de dimensões na topologia hierárquica. 

Mas geralmente nem todas as combinações de dimensões são válidas, o que pode reduzir significativamente a complexidade. Atualmente, se os utilizadores agregarem a métrica em si, não limitamos o número de dimensões. Se precisar de utilizar a funcionalidade rollup fornecida pelo Metrics Advisor, o número de dimensões não deve ser superior a 6. No entanto, limitamos o número de séries de tempo expandidas por dimensões para uma métrica a menos de 10.000.

A ferramenta **da árvore incidente** na página de diagnóstico apenas mostra nós onde foi detetada uma anomalia, em vez de toda a topologia. Isto é para ajudá-lo a se concentrar na questão atual. Também pode não mostrar todas as anomalias dentro da métrica, e em vez disso apresentará as anomalias de topo com base na contribuição. Desta forma, podemos rapidamente descobrir o impacto, o âmbito e o caminho de propagação dos dados anormais. O que reduz significativamente o número de anomalias em que precisamos de nos concentrar e ajuda os utilizadores a compreender e localizar os seus principais problemas. 
 
Por exemplo, quando ocorre uma `Service = S2 | Data Center = DC2 | Machine = M5` anomalia, o desvio da anomalia afeta o nó-mãe `Service= S2` que também detetou a anomalia, mas a anomalia não afeta todo o centro de dados `DC2` em e todos os serviços em `M5` . A árvore incidente seria construída como na imagem abaixo, a anomalia superior é capturada `Service = S2` , e a causa raiz poderia ser analisada em dois caminhos que ambos levam a `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 vértices rotulados com dois caminhos distintos ligados por bordas com um nó comum marcado como S2. A anomalia superior é capturada no Serviço = S2, e a causa raiz pode ser analisada pelos dois caminhos que ambos levam ao Serviço = S2 ! Data Center = DC2 Máquina = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>Passos Seguintes
- [Métricas Conselheiro Visão Geral](overview.md)
- [Utilizar o portal Web](quickstarts/web-portal.md)