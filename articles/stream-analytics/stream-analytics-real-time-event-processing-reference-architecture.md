---
title: Processamento de eventos em tempo real utilizando o processamento de eventos do Azure Stream Analytics
description: Este artigo descreve a arquitetura de referência para alcançar o processamento de eventos em tempo real e análises com o Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 7f9748a4e4f1c86362781aa80d8958237c97106a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816958"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Arquitetura de referência: Eventos em tempo real de processamento com o Microsoft Azure Stream Analytics
Destina-se a arquitetura de referência para o Azure Stream Analytics de processamento de eventos em tempo real para fornecer um plano gráfico genérico para implementar uma plataforma em tempo real como uma solução de processamento de fluxo de serviço (PaaS) com o Microsoft Azure.

## <a name="summary"></a>Resumo
Tradicionalmente, soluções de análise tem sido com base em recursos, como ETL (extração, transformação e carga) e o armazenamento de dados, onde os dados são armazenados antes da análise. Necessidades em constante mudança, incluindo o mais rapidamente que são recebidos dados, estão a ser usada nesse modelo existente para o limite. A capacidade de analisar os dados dentro de mover fluxos antes de armazenamento é uma solução e, embora não seja uma nova funcionalidade, a abordagem possui não foi amplamente adotada em todos os mercados de setor. 

O Microsoft Azure fornece um catálogo extenso de tecnologias de análise que são capazes de suportar uma matriz de requisitos e cenários de solução diferente. Selecionar os serviços do Azure para implementar uma solução ponto a ponto pode ser um desafio por conta da diversidade de ofertas. Este documento foi concebido para descrever as capacidades e a interoperação de vários serviços do Azure que suportam uma solução de transmissão de eventos. Também explica alguns dos cenários em que os clientes podem se beneficiar deste tipo de abordagem.

## <a name="contents"></a>Conteúdos
* Resumo Executivo
* Introdução à análise em tempo real
* Proposta de valor de dados em tempo real no Azure
* Cenários comuns para análise em tempo real
* Arquitetura e componentes
  * Origens de Dados
  * Camada de integração de dados
  * Camada de análise em tempo real
  * Camada de armazenamento de dados
  * Apresentação / consumo de camada
* Conclusão

**Autor:** Centro de informações de dados de Charles Feddersen, Arquiteto de soluções, de excelência, Microsoft Corporation

**Publicado:** Janeiro de 2015

**Revisão:** 1.0

**Transferir:** [Eventos em tempo real de processamento com o Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

