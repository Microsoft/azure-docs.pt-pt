---
title: Soluções que usam análises distribuídas globalmente no Azure Cosmos DB.
description: Saiba mais sobre as soluções que podem ser criadas usando a análise distribuída globalmente no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d50b946e67bbcc171850b71021165356011f58e3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755206"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Soluções que usam análises distribuídas globalmente no Azure Cosmos DB

Este artigo descreve as soluções que podem ser criadas usando a análise distribuída globalmente no Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Bens de varejo e de consumo

Você pode usar o suporte do Spark no Azure Cosmos DB para fornecer recomendações e ofertas em tempo real. Você pode ajudar os clientes a descobrir os itens necessários com personalização em tempo real e recomendações de produtos.

* Você pode usar o suporte interno de Machine Learning fornecido pelo Apache Spark Runtime para gerar recomendações em tempo real em todos os catálogos de produtos.

* Você pode clicar em transmitir dados, comprar dados e dados do cliente para fornecer recomendações direcionadas que conduzem o valor de tempo de vida.

* Usando o recurso de distribuição global do Azure Cosmos DB, grandes volumes de dados do produto que são distribuídos entre regiões podem ser analisados em milissegundos.

* Você pode obter informações rapidamente para usuários e dados distribuídos geograficamente. Você pode melhorar a taxa de conversão de promoção servindo o anúncio correto para o usuário correto no momento certo.

* Você pode aproveitar o recurso de streaming do Spark embutido para enriquecer dados dinâmicos, combinando-os com dados estáticos do cliente. Dessa forma, você pode fornecer anúncios mais personalizados e direcionados em tempo real e em contexto com o que os clientes estão fazendo.

A imagem a seguir mostra como Azure Cosmos DB suporte do Spark é usado para otimizar o preço e as promoções:

![Suporte ao Spark do Azure Cosmos DB para otimizar o preço e as promoções](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


A imagem a seguir mostra como Azure Cosmos DB suporte do Spark é usado no mecanismo de recomendação em tempo real:

![Suporte ao Azure Cosmos DB Spark no mecanismo de recomendação em tempo real](./media/spark-api-introduction/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Manufatura e IoT

A plataforma de análise interna do Azure Cosmos DB permite que você habilite a análise em tempo real de dados de IoT de milhões de dispositivos em escala global. Você pode fazer inovações modernas como prever padrões meteorológicos, análise preditiva e otimizações de energia.

* Usando Azure Cosmos DB, você pode minar dados como métricas de ativos em tempo real e fatores meteorológicos e, em seguida, aplicar análise de grade inteligente para otimizar o desempenho de dispositivos conectados no campo. O Smart Grid Analytics é a chave para controlar os custos operacionais, melhorar a confiabilidade da grade e fornecer serviços de energia personalizados aos consumidores.

A imagem a seguir mostra como o suporte ao Spark do Azure Cosmos DB é usado para ler métricas de dispositivos IoT e aplicar análise de grade inteligente:

![Suporte do Spark do Azure Cosmos DB para ler métricas de dispositivos IoT](./media/spark-api-introduction/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Manutenção preditiva

* A manutenção de ativos, como compactadores, que são usados em pequenas Rigs de análise para plataformas de água profunda, é um empreendimento complexo. Esses ativos estão localizados em todo o mundo e geram petabytes de dados. Usando Azure Cosmos DB, você pode criar um pipeline de dados de previsão de ponta a ponta que usa o streaming do Spark para processar grandes quantidades de telemetria de sensor, armazenar partes de ativos e dados de mapeamentos de sensor.

* Você pode criar e implantar modelos de aprendizado de máquina para prever falhas de ativo antes que elas ocorram e emitir as ordens de trabalho de manutenção antes que a falha ocorra.

A imagem a seguir mostra como o suporte ao Spark do Azure Cosmos DB é usado para criar um sistema de manutenção preditiva:

![O suporte ao Spark do Azure Cosmos DB para criar um sistema de manutenção preditiva](./media/spark-api-introduction/predictive-maintenance-system.png)

A imagem a seguir mostra como o suporte ao Spark do Azure Cosmos DB é usado para criar um sistema de diagnóstico de veículo em tempo real:

![O suporte ao Spark do Azure Cosmos DB para criar um sistema de diagnóstico de veículo em tempo real](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Jogos

* Com o suporte interno ao Spark, o Azure Cosmos DB permite que você crie, dimensione e implante facilmente modelos de análise avançada e de aprendizado de máquina em minutos para criar a melhor experiência de jogos possível.

* Você pode analisar o Player, a compra e os dados comportamentais para criar ofertas personalizadas relevantes para obter altas taxas de conversão.

* Usando o Spark Machine Learning, você pode analisar e obter informações sobre os dados de telemetria do jogo. Você pode diagnosticar e impedir tempos de carregamento lentos e problemas no jogo.

A imagem a seguir mostra como o suporte ao Spark do Azure Cosmos DB é usado no Game Analytics:

![Suporte ao Spark do Azure Cosmos DB no Game Analytics](./media/spark-api-introduction/gaming-analytics.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os benefícios do Azure Cosmos DB, consulte o artigo [visão geral](introduction.md) .
* [Introdução à API de Azure Cosmos DB para MongoDB](mongodb-introduction.md)
* [Introdução ao Azure Cosmos DB API do Cassandra](cassandra-introduction.md)
* [Introdução à API do Azure Cosmos DB Gremlin](graph-introduction.md)
* [Introdução ao Azure Cosmos DB API de Tabela](table-introduction.md)
