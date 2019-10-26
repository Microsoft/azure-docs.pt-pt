---
title: Processamento de eventos em tempo real usando Azure Stream Analytics
description: Este artigo descreve a arquitetura de referência para obter análise e processamento de eventos em tempo real usando o Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 21a0e4e468b606ec7bb7e33bf1a616e68cd6cf50
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925101"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Arquitetura de referência: processamento de eventos em tempo real com Microsoft Azure Stream Analytics
A arquitetura de referência para o processamento de eventos em tempo real com Azure Stream Analytics destina-se a fornecer um plano gráfico genérico para a implantação de uma solução de processamento de fluxo de PaaS (plataforma como serviço) em tempo real com Microsoft Azure.

## <a name="summary"></a>Resumo
Tradicionalmente, as soluções de análise têm sido baseadas em recursos como ETL (extração, transformação, carregamento) e data warehousing, em que os dados são armazenados antes da análise. A alteração dos requisitos, incluindo dados mais rápidos, está enviando por push esse modelo existente para o limite. A capacidade de analisar dados na movimentação de fluxos antes do armazenamento é uma solução e, embora não seja um novo recurso, a abordagem não foi amplamente adotada em todos os verticais do setor. 

O Microsoft Azure fornece um catálogo abrangente de tecnologias de análise que são capazes de dar suporte a uma matriz de diferentes cenários e requisitos de solução. Selecionar quais serviços do Azure implantar para uma solução de ponta a ponta pode ser um desafio devido à amplitude das ofertas. Este documento foi projetado para descrever os recursos e a interoperação dos vários serviços do Azure que dão suporte a uma solução de streaming de eventos. Ele também explica alguns dos cenários nos quais os clientes podem se beneficiar desse tipo de abordagem.

## <a name="contents"></a>Conteúdos
* Resumo executivo
* Introdução à análise em tempo real
* Proposta de valor de dados em tempo real no Azure
* Cenários comuns de análise em tempo real
* Arquitetura e componentes
  * Origens de Dados
  * Camada de integração de dados
  * Camada de análise em tempo real
  * Camada de armazenamento de dados
  * Camada de apresentação/consumo
* Conclusão

**Autor:** Charles Feddersen, arquiteto de soluções, centro de excelência de informações de dados, Microsoft Corporation

**Publicado em:** Janeiro de 2015

**Revisão:** 1,0

**Download:** [processamento de eventos em tempo Real com Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente o [Fórum de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

