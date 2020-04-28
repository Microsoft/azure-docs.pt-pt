---
title: Soluções que utilizam análises distribuídas globalmente no Azure Cosmos DB.
description: Conheça as soluções que podem ser construídas utilizando a análise distribuída globalmente no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73681796"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Soluções que utilizam análises distribuídas globalmente no Azure Cosmos DB

Este artigo descreve as soluções que podem ser construídas usando a análise distribuída globalmente no Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Bens de retalho e de consumo

Você pode usar o suporte spark em Azure Cosmos DB para fornecer recomendações e ofertas em tempo real. Pode ajudar os clientes a descobrir os itens que vão precisar com personalização em tempo real e recomendações de produtos.

* Pode utilizar o suporte integrado de Machine Learning fornecido pelo tempo de execução da Apache Spark para gerar recomendações em tempo real nos catálogos de produtos.

* Pode extrair dados de fluxo de cliques, dados de compra e dados do cliente para fornecer recomendações direcionadas que impulsionam o valor vitalício.

* Utilizando a funcionalidade de distribuição global do Azure Cosmos DB, grandes volumes de dados de produtos que se espalham por regiões podem ser analisados em milissegundos.

* Pode obter rapidamente informações para os utilizadores e dados distribuídos geograficamente. Pode melhorar a taxa de conversão de promoção servindo o anúncio certo ao utilizador certo no momento certo.

* Pode aproveitar a capacidade de streaming de Spark incorporada para enriquecer dados ao vivo, combinando-os com dados estáticos do cliente. Desta forma, pode entregar anúncios mais personalizados e direcionados em tempo real e em contexto com o que os clientes estão a fazer.

A imagem que se segue mostra como o suporte da Azure Cosmos DB Spark é usado para otimizar preços e promoções:

![Suporte spark da Azure Cosmos DB para otimizar preços e promoções](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


A imagem que se segue mostra como o suporte da Azure Cosmos DB Spark é utilizado no motor de recomendação em tempo real:

![Suporte da Azure Cosmos DB Spark no motor de recomendação em tempo real](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Fabricação e IoT

A plataforma de análise incorporada da Azure Cosmos DB permite-lhe permitir a análise em tempo real de dados ioT de milhões de dispositivos à escala global. Você pode fazer inovações modernas como prever padrões climáticos, análise supreventiva e otimizações de energia.

* Ao utilizar o Azure Cosmos DB, pode extrair dados como métricas de ativos em tempo real e fatores climáticos, aplicando então análise seletiva para otimizar o desempenho de dispositivos conectados no campo. A análise da rede inteligente é a chave para controlar os custos operacionais, melhorar a fiabilidade da rede e fornecer serviços energéticos personalizados aos consumidores.

A imagem que se segue mostra como o suporte spark da Azure Cosmos DB é usado para ler métricas a partir de dispositivos IoT e aplicar análises inteligentes de grelha:

![Suporte de Faísca da Azure Cosmos DB para ler métricas a partir de dispositivos IoT](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Manutenção preditiva

* Manter ativos como compressores que são usados em pequenas plataformas de perfuração para plataformas de águas profundas é um esforço complexo. Estes ativos estão localizados em todo o mundo e geram petabytes de dados. Ao utilizar o Azure Cosmos DB, pode construir um pipeline de dados preditivos de ponta a ponta que utiliza o streaming Spark para processar grandes quantidades de telemetria de sensores, peças de ativos de loja e dados de mapeamento de sensores.

* Pode construir e implementar modelos de aprendizagem automática para prever falhas de ativos antes de acontecerem e emitir ordens de manutenção antes da falha ocorrer.

A imagem que se segue mostra como o suporte spark da Azure Cosmos DB é usado para construir um sistema de manutenção preditiva:

![Suporte de Faísca da Azure Cosmos DB para construir um sistema de manutenção preditiva](./media/analytics-solution-architectures/predictive-maintenance-system.png)

A imagem que se segue mostra como o suporte de Spark da Azure Cosmos DB é usado para construir um sistema de diagnóstico de veículos em tempo real:

![Suporte de Faísca da Azure Cosmos DB para construir um sistema de diagnóstico de veículos em tempo real](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Jogos

* Com suporte à Spark incorporado, o Azure Cosmos DB permite-lhe construir, escalar e implementar modelos avançados de análise e machine learning em minutos para construir a melhor experiência de jogo possível.

* Pode analisar dados de jogadores, compras e comportamentais para criar ofertas personalizadas relevantes para atingir altas taxas de conversão.

* Utilizando a aprendizagem automática Spark, pode analisar e obter insights sobre os dados da telemetria do jogo. Pode diagnosticar e prevenir tempos de carga lentas e problemas no jogo.

A imagem que se segue mostra como o suporte spark da Azure Cosmos DB é usado na análise de jogos:

![Suporte spark da Azure Cosmos DB na análise de jogos](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Passos seguintes

* Para conhecer os benefícios do Azure Cosmos DB, consulte o artigo sobre [visão geral.](introduction.md)
* [Começar com o Azure Cosmos DB API para MongoDB](mongodb-introduction.md)
* [Começar com a API Do Cosmos Azure DB Cassandra](cassandra-introduction.md)
* [Começar com a API Azure Cosmos DB Gremlin](graph-introduction.md)
* [Começar com a Mesa DD D DD IA do Azure Cosmos](table-introduction.md)
