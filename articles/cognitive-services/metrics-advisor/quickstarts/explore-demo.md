---
title: Comece a explorar a demonstração do Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a interface web Metrics Advisor usando a demonstração que fornecemos
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 39748bacf784af0b3f162f960cd7d87cbd03a2eb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047595"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>Quickstart: Explore a demonstração do Metrics Advisor com dados de exemplo

> [!Note]
> A demonstração do Metrics Advisor é apenas para leitura, e não poderá embarcar nos seus dados. Para utilizar o serviço nos seus dados, crie primeiro [um recurso Metrics Advisor](web-portal.md).

Utilize este artigo para começar rapidamente a explorar as principais funcionalidades no Metrics Advisor. Fornecemos um site de demonstração com dados de amostra e configurações predefinidas, para que você se familiarize com o portal Web totalmente apresentado.

Clique [neste link](https://aka.ms/MetricsAdvisor/Demo) para ir ao site de demonstração.

## <a name="view-the-available-sample-data"></a>Ver os dados da amostra disponíveis

Depois de iniciar sôm no site de demonstração, verá uma página **de feed de dados.** um feed de dados é um grupo lógico de dados de séries de tempo, que é consultado a partir da sua fonte de dados. Para obter mais informações sobre os termos e conceitos utilizados no Metrics Advisor, consulte o [Glossário](../glossary.md). 

Existem vários feeds de dados listados, que são ingeridos de diferentes tipos de fontes de dados, tais como a base de dados Azure SQL ou a Tabela Azure. Cada um usa configurações de configuração ligeiramente diferentes para se ligar às lojas de dados associadas.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="Lista de dados da amostra" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>Explore as configurações do feed de dados

Clique na amostra - Custo/Receita - Feed de dados *cidade/categoria.* Você verá várias secções de detalhes para o feed:

* Data feed nome e estado de ingestão.
* Uma lista de métricas consultadas a partir da fonte de dados. Por exemplo, *custos* e *receitas.* 
* Histórico de alerta para quando o feed de dados ficar indisponível. 
* Registos de quando o feed de dados foi atualizado.   
* Informações e configurações de feed de dados.

:::image type="content" source="../media/data-feed-view.png" alt-text="Lista de dados da amostra" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>Ver visualizações e configurações de séries de tempo

Clique na métrica de *custos* na amostra - Custo/Receita - Feed de dados *cidade/categoria.* Você verá as séries de tempo associadas cortadas por dimensões, com visualizações de acordo com os dados métricos históricos. A banda azul em torno dos dados métricos representa a gama de valor esperada dos modelos de machine learning do Metrics Advisor. Os pontos que caem fora desta banda serão marcados como pontos vermelhos, que são anomalias detetadas. 

:::image type="content" source="../media/series-visualization.png" alt-text="Lista de dados da amostra" lightbox="../media/series-visualization.png":::

A deteção de anomalias é configurável afinando as **configurações de deteção** no lado esquerdo da página de detalhes métricos. Vários métodos de deteção de anomalias estão disponíveis e pode combiná-los. Pode ainda experimentar diferentes sensibilidades, indicação de direções e outras configurações. A **ligação de configuração Avançada** na parte inferior das configurações de **deteção** permite criar configurações de deteção mais complexas e personalizadas, que podem ser usadas em grupos ou séries individuais. 

Também é possível sintonizar a deteção de anomalias fornecendo feedback ao algoritmo de deteção. Clique numa anomalia e use o painel **de feedback Add** para configurar o seu estado de anomalia, sazonalidade e alteração do estado do ponto. Este feedback será incorporado na deteção de pontos futuros.  

Na parte inferior do painel de **feedback Add,** há uma ligação do hub de **incidentes**, que irá direcioná-lo para a página de análise de incidentes e analisar a causa do incidente.  

:::image type="content" source="../media/incident-link.png" alt-text="Lista de dados da amostra" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>Explore os resultados da deteção de anomalias e realize análises de causa de raiz

Quando clicar no link do hub de **incidentes** a partir de uma anomalia, verá uma página de análise de incidentes, contendo insights de diagnóstico sobre o incidente, tais como Severidade, o número de anomalias envolvidas e tempo de início/fim. A secção **de causa-raiz** apresenta conselhos automatizados analisando a árvore incidente, tendo em conta: desvio, distribuição e contribuição para anomalias dos pais, que podem ser a causa principal do incidente.

A secção **de Diagnóstico** mostra uma árvore do incidente, juntamente com várias guias para diagnosticar o incidente.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Lista de dados da amostra" lightbox="../media/incident-diagnostic.png":::

Ao identificar a causa do incidente, pode tomar medidas e mitigar o problema antes que a situação piore. Também pode explorar mais insights clicando nas outras funcionalidades de diagnóstico fornecidas. 

## <a name="next-steps"></a>Passos seguintes

- [Utilizar o portal Web](web-portal.md)
- [Utilizar a API REST](rest-api.md)
- [A bordo dos feeds dos seus dados](../how-tos/onboard-your-data.md)
    - [Gerir feeds de dados](../how-tos/manage-data-feeds.md)
    - [Configurações para diferentes fontes de dados](../data-feeds-from-different-sources.md)
