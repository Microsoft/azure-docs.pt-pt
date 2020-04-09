---
title: Rastreio distribuído em Insights de Aplicação Azure [ Microsoft Docs
description: Fornece informações sobre o suporte da Microsoft para rastreio distribuído através da nossa parceria no projeto OpenCensus
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 83575aa7f9b63615f453e00bd06b00a5540b9a9e
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892262"
---
# <a name="what-is-distributed-tracing"></a>O que é Rastreio Distribuído?

O advento das arquiteturas modernas de nuvem e [microserviços](https://azure.com/microservices) deu origem a serviços simples e independentes que podem ajudar a reduzir custos, ao mesmo tempo que aumentam a disponibilidade e a entrada. Mas, embora estes movimentos tenham facilitado a compreensão dos serviços individuais como um todo, tornaram os sistemas globais mais difíceis de raciocinar e depurar.

Em arquiteturas monolíticas, habituámo-nos a depurar com pilhas de chamadas. As pilhas de chamadas são ferramentas brilhantes para mostrar o fluxo de execução (método A chamado Método B, que chamou método C), juntamente com detalhes e parâmetros sobre cada uma dessas chamadas. Isto é ótimo para monólitos ou serviços que executam num único processo, mas como depuramos quando a chamada é transversal a um limite de processo, e não apenas uma referência na pilha local? 

É aí que entra o rastreio distribuído.  

O rastreio distribuído é o equivalente a pilhas de chamadas para arquiteturas modernas de nuvem e microserviços, com a adição de um perfil de desempenho simplista. No Monitor Azure, fornecemos duas experiências para consumir dados de vestígios distribuídos. A primeira é a nossa visão de [diagnóstico de transações,](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) que é como uma pilha de chamadas com uma dimensão temporal adicionada. A visão de diagnóstico de transações proporciona visibilidade numa única transação/pedido, e é útil para encontrar a causa principal de problemas de fiabilidade e estrangulamentos de desempenho por pedido.

O Azure Monitor também oferece uma visão do mapa de [aplicações](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) que agrega muitas transações para mostrar uma visão topológica de como os sistemas interagem, e quais são as taxas médias de desempenho e erro. 

## <a name="how-to-enable-distributed-tracing"></a>Como ativar o rastreio distribuído

Permitir o rastreio distribuído pelos serviços numa aplicação é tão simples como adicionar o agente adequado, SDK ou biblioteca a cada serviço, com base na linguagem em que o serviço foi implementado.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Habilitar através de Insights de Aplicação através de instrumentação automática ou SDKs

Os agentes de Insights de Aplicação e/ou SDKs para .NET Core, Java, Node.js e JavaScript todos os suportes distribuídos de forma nativa. As instruções para a instalação e configuração de cada aplicação Insights SDK estão disponíveis abaixo:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

Com a aplicação adequada Insights SDK instalada e configurada, a informação de rastreio é recolhida automaticamente para quadros populares, bibliotecas e tecnologias por auto-coleccionadores de dependência sdK. A lista completa de tecnologias suportadas está disponível na documentação de [recolha automática dependency.](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)

 Além disso, qualquer tecnologia pode ser rastreada manualmente com uma chamada para [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) no [TelemettryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Ativar via OpenCensus

Além dos SDKs de Insights de Aplicação, a Application Insights também suporta o rastreio distribuído através do [OpenCensus](https://opencensus.io/). OpenCensus é uma distribuição única de bibliotecas de código aberto, fornecedor-agnóstico, para fornecer recolha de métricas e rastreio distribuído para serviços. Também permite à comunidade de código aberto permitir rastreios distribuídos com tecnologias populares como Redis, Memcached ou MongoDB. A [Microsoft colabora no OpenCensus com vários outros parceiros de monitorização e nuvem.](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)

[Python](opencensus-python.md) 

O site OpenCensus mantém documentação de referência da API para [Python](https://opencensus.io/api/python/trace/usage.html) e [Go,](https://godoc.org/go.opencensus.io)bem como vários guias diferentes para a utilização do OpenCensus. 

## <a name="next-steps"></a>Passos seguintes

* [Guia de utilização OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa de aplicações](./../../azure-monitor/app/app-map.md)
* [Monitorização do desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)
