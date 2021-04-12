---
title: Rastreio distribuído em Azure Application Insights | Microsoft Docs
description: Fornece informações sobre o suporte da Microsoft para rastreios distribuídos através da nossa parceria no projeto OpenCensus
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a47b41a8b7f4e18be58c32c97cf279b9229f26da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579508"
---
# <a name="what-is-distributed-tracing"></a>O que é Rastreio Distribuído?

O advento das arquiteturas modernas de nuvem e [microserviços](https://azure.com/microservices) deu origem a serviços simples e independentes que podem ajudar a reduzir custos, ao mesmo tempo que aumentam a disponibilidade e a produção. Mas, embora estes movimentos tenham facilitado a compreensão dos serviços individuais como um todo, tornaram os sistemas globais mais difíceis de raciocinar e depurar.

Em arquiteturas monolíticas, habituámo-nos a depurar com pilhas de chamadas. As pilhas de chamadas são ferramentas brilhantes para mostrar o fluxo de execução (método A chamado Método B, que chamou método C), juntamente com detalhes e parâmetros sobre cada uma dessas chamadas. Isto é ótimo para monólitos ou serviços em execução num único processo, mas como depuramos quando a chamada está através de um limite de processo, e não apenas uma referência na pilha local? 

É aí que entra o rastreio distribuído.  

O rastreio distribuído é o equivalente a pilhas de chamadas para arquiteturas modernas de nuvem e microserviços, com a adição de um profiler de desempenho simplista lançado dentro No Azure Monitor, fornecemos duas experiências para consumir dados de traços distribuídos. A primeira é a nossa visão [de diagnóstico de transação,](./transaction-diagnostics.md) que é como uma pilha de chamadas com uma dimensão temporal adicionada. A visão de diagnóstico de transação proporciona visibilidade numa única transação/pedido, e é útil para encontrar a causa principal de problemas de fiabilidade e estrangulamentos de desempenho numa base de pedido.

O Azure Monitor também oferece uma visão de mapa de [aplicações](./app-map.md) que agrega muitas transações para mostrar uma visão topológica de como os sistemas interagem, e quais são as taxas médias de desempenho e erro. 

## <a name="how-to-enable-distributed-tracing"></a>Como permitir o rastreio distribuído

Permitir a distribuição de rastreios através dos serviços numa aplicação é tão simples como adicionar o agente adequado, SDK ou biblioteca a cada serviço, com base na linguagem em que o serviço foi implementado.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Habilitação através de Insights de Aplicação através de auto-instrumentação ou SDKs

Os agentes de Insights de Aplicação e/ou SDKs para .NET, .NET Core, Java, Node.js e JavaScript todos os rastreios distribuídos de forma nativa. Instruções para instalar e configurar cada SDK de Insights de Aplicação estão disponíveis abaixo:

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

Com a aplicação adequada Insights SDK instalada e configurada, a informação de rastreio é automaticamente recolhida para quadros, bibliotecas e tecnologias populares por coletores de auto-coleccionadores de dependência SDK. A lista completa de tecnologias suportadas está disponível [na documentação de recolha automática da Dependência.](./auto-collect-dependencies.md)

 Além disso, qualquer tecnologia pode ser rastreada manualmente com uma chamada para [TrackDependency](./api-custom-events-metrics.md) no [TelemetriaClient](./api-custom-events-metrics.md).

## <a name="enable-via-opencensus"></a>Ativar via OpenCensus

Além dos SDKs de Insights de Aplicação, o Application Insights também suporta o rastreio distribuído através [do OpenCensus.](https://opencensus.io/) O OpenCensus é uma distribuição única de bibliotecas de fonte aberta, agnóstica e única para fornecer recolha de métricas e rastreio distribuído para serviços. Também permite que a comunidade de código aberto permita o rastreio distribuído com tecnologias populares como Redis, Memcached ou MongoDB. [A Microsoft colabora no OpenCensus com vários outros parceiros de monitorização e nuvem.](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)

[Python](opencensus-python.md) 

O site OpenCensus mantém documentação de referência da API para [Python](https://opencensus.io/api/python/trace/usage.html) e [Go,](https://godoc.org/go.opencensus.io)bem como vários guias diferentes para a utilização do OpenCensus. 

## <a name="next-steps"></a>Passos seguintes

* [Guia de utilização do OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa de aplicação](./app-map.md)
* [Monitorização de desempenho de ponta a ponta](../app/tutorial-performance.md)

