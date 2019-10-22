---
title: Rastreamento distribuído no insights do Aplicativo Azure | Microsoft Docs
description: Fornece informações sobre o suporte da Microsoft para rastreamento distribuído por meio de nosso encaminhador local e parceria no projeto OpenCensus
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0b51617966ad85037ad29f3e5005a17f66602e01
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677127"
---
# <a name="what-is-distributed-tracing"></a>O que é o rastreamento distribuído?

O advento das arquiteturas modernas de nuvem e de [microserviço](https://azure.com/microservices) deu origem aos serviços simples e implantáveis de forma independente que podem ajudar a reduzir os custos enquanto aumentam a disponibilidade e a taxa de transferência. Mas embora esses movimentos tenham tornado serviços individuais mais fáceis de entender como um todo, eles tornaram os sistemas em geral mais difíceis de descobrir e depurar.

Em arquiteturas monolíticos, estamos acostumados a depurar com pilhas de chamadas. As pilhas de chamadas são ferramentas brilhantes para mostrar o fluxo de execução (o método A chamou o método B, que chamou o método C), juntamente com detalhes e parâmetros sobre cada uma dessas chamadas. Isso é ótimo para os monolíticos ou serviços em execução em um único processo, mas como Depuramos quando a chamada está em um limite de processo, e não simplesmente uma referência na pilha local? 

É aí que entra o rastreamento distribuído.  

O rastreamento distribuído é o equivalente a pilhas de chamadas para arquiteturas de microserviço e nuvem modernas, com a adição de um criador de perfil de desempenho simplista lançado no. No Azure Monitor, fornecemos duas experiências para consumir dados de rastreamento distribuídos. A primeira é nossa exibição de [diagnóstico de transação](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) , que é como uma pilha de chamadas com uma dimensão de tempo adicionada no. A exibição de diagnóstico de transação fornece visibilidade em uma única transação/solicitação e é útil para encontrar a causa raiz de problemas de confiabilidade e gargalos de desempenho de acordo com a solicitação.

O Azure Monitor também oferece uma exibição de [mapa do aplicativo](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) que agrega muitas transações para mostrar uma exibição topológica de como os sistemas interagem e quais são as taxas médias de desempenho e de erro. 

## <a name="how-to-enable-distributed-tracing"></a>Como habilitar o rastreamento distribuído

Habilitar o rastreamento distribuído entre os serviços em um aplicativo é tão simples quanto adicionar o SDK ou a biblioteca apropriada a cada serviço, com base no idioma em que o serviço foi implementado.

## <a name="enabling-via-application-insights-sdks"></a>Habilitando o via SDKs de Application Insights

Os SDKs do Application Insights para .NET, .NET Core, Java, Node. js e JavaScript oferecem suporte ao rastreamento distribuído nativamente. As instruções para instalar e configurar cada SDK do Application Insights estão disponíveis abaixo:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

Com o SDK do Application Insights adequado instalado e configurado, as informações de rastreamento são coletadas automaticamente para estruturas, bibliotecas e tecnologias populares por coletores automáticos de dependências do SDK. A lista completa de tecnologias com suporte está disponível na [documentação da coleção de dependências automáticas](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Além disso, qualquer tecnologia pode ser controlada manualmente com uma chamada para [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) no [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Habilitar via OpenCensus

Além dos SDKs de Application Insights, o Application Insights também dá suporte ao rastreamento distribuído por meio do [OpenCensus](https://opencensus.io/). O OpenCensus é uma distribuição única de software livre, independente de fornecedor, de bibliotecas para fornecer coleta de métricas e rastreamento distribuído para serviços. Ele também permite que a comunidade de software livre habilite o rastreamento distribuído com tecnologias populares como Redis, memcached ou MongoDB. [A Microsoft colabora em OpenCensus com vários outros parceiros de monitoramento e de nuvem](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Para adicionar recursos de rastreamento distribuído a um aplicativo com OpenCensus, primeiro [Instale e configure o Application insights encaminhador local](./../../azure-monitor/app/opencensus-local-forwarder.md). A partir daí, configure o OpenCensus para rotear dados de rastreamento distribuídos por meio do encaminhador local. Há suporte para [Python](./../../azure-monitor/app/opencensus-python.md) e [go](./../../azure-monitor/app/opencensus-go.md) .

O site do OpenCensus mantém a documentação de referência de API para [Python](https://opencensus.io/api/python/trace/usage.html) e [go](https://godoc.org/go.opencensus.io), bem como vários guias diferentes para usar o OpenCensus. 

## <a name="next-steps"></a>Passos seguintes

* [Guia de uso do OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)
