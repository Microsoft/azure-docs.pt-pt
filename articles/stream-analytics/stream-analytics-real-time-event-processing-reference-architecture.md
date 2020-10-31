---
title: Processamento de eventos em tempo real usando Azure Stream Analytics
description: Este artigo descreve a arquitetura de referência para alcançar o processamento e análise de eventos em tempo real usando a Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 0aa489e1732a8be564b3cefe1ed74d7aaacb1456
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124429"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Arquitetura de referência: Processamento de eventos em tempo real com o Microsoft Azure Stream Analytics
A arquitetura de referência para o processamento de eventos em tempo real com o Azure Stream Analytics destina-se a fornecer uma planta genérica para implementar uma plataforma em tempo real como uma solução de processamento de streaming de serviço (PaaS) com o Microsoft Azure.

## <a name="summary"></a>Resumo
Tradicionalmente, as soluções de análise têm sido baseadas em capacidades como ETL (extrato, transformação, carga) e armazenamento de dados, onde os dados são armazenados antes da análise. A alteração dos requisitos, incluindo dados mais rápidos, está a levar este modelo existente ao limite. A capacidade de analisar dados dentro de fluxos em movimento antes do armazenamento é uma solução, e embora não seja uma nova capacidade, a abordagem não foi amplamente adotada em todas as verticais da indústria. 

O Microsoft Azure fornece um extenso catálogo de tecnologias de análise que são capazes de suportar uma variedade de diferentes cenários e requisitos de solução. Selecionar quais os serviços da Azure para implementar para uma solução de ponta a ponta pode ser um desafio dada a amplitude das ofertas. Este trabalho é projetado para descrever as capacidades e interoperação dos vários serviços Azure que suportam uma solução de streaming de eventos. Também explica alguns dos cenários em que os clientes podem beneficiar deste tipo de abordagem.

## <a name="contents"></a>Conteúdos
* Resumo Executivo
* Introdução à Real-Time Analytics
* Proposta de valor de dados de Real-Time em Azure
* Cenários Comuns para Real-Time Analytics
* Arquitetura e Componentes
  * Origens de Dados
  * Camada Data-Integration
  * Camada de análise em tempo real
  * Camada de armazenamento de dados
  * Apresentação / Camada de Consumo
* Conclusão

**Autor:** Charles Feddersen, Solution Architect, Data Insights Center of Excellence, Microsoft Corporation

**Publicado:** janeiro de 2015

**Revisão:** 1.0

**Download:** [Processamento de eventos em tempo real com o Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente a [página de perguntas do Microsoft Q&A para a Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)