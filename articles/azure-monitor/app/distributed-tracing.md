---
title: Distribuído rastreio no Application Insights do Azure | Documentos da Microsoft
description: Fornece informações sobre o suporte da Microsoft para o rastreio distribuído por meio de nosso reencaminhador local e a parceria no projeto OpenCensus
services: application-insights
keywords: ''
author: nikmd23
ms.author: nimolnar
ms.reviewer: mbullwin
ms.date: 09/17/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7bc04748f2a5b8caa8f589140dd46f0650b7b390
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60898853"
---
# <a name="what-is-distributed-tracing"></a>O que é distribuída rastreio?

O advento da nuvem moderna e [microsserviços](https://azure.com/microservices) arquiteturas tem deu origem a simples, implementáveis independentemente serviços que podem ajudar a reduzir os custos, aumentar a disponibilidade e débito. Mas, embora estes movimentos tem feito serviços individuais mais fáceis de entender como um todo, que cometeu sistemas geral mais difícil de ponderar e depurar.

Nas arquiteturas monolíticas, obteve a usamos para a depuração com pilhas de chamadas. Pilhas de chamadas são ferramentas brilhante para mostrar o fluxo de execução (método A chamada B de método, que chama o método C), juntamente com os detalhes e parâmetros sobre cada uma dessas chamadas. Isso é ótimo para monolitos ou serviços em execução num único processo, mas como vamos depurar quando a chamada é através de um limite de processo, não simplesmente uma referência na pilha local? 

É aí que entra rastreio distribuído.  

Rastreio distribuído é o equivalente de pilhas de chamadas para modernas arquiteturas de microsserviços e na cloud, com a adição de um criador de perfil de desempenho simplista emitida no. No Azure Monitor, podemos fornecer duas experiências para o consumo de dados de rastreio distribuído. A primeira é nosso [diagnósticos de transação](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) exibição, que é como uma pilha de chamadas com uma dimensão de tempo, adicionado no. A vista de diagnóstico de transação fornece visibilidade numa única transação/solicitação e é útil para encontrar a causa raiz dos problemas de confiabilidade e afunilamentos de desempenho numa base por solicitação.

Monitor do Azure também oferece um [mapa da aplicação](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) exibição que agrega muitas transações para mostrar uma vista topológica do como os sistemas interagem e quais são as taxas de desempenho e de erros média. 

## <a name="how-to-enable-distributed-tracing"></a>Como ativar o rastreio distribuído

Ativar o rastreio distribuído entre os serviços num aplicativo é tão simples quanto adicionar o SDK ou biblioteca para cada serviço adequado com base na linguagem de que o serviço foi implementado em.

## <a name="enabling-via-application-insights-sdks"></a>Ativar através de SDKs do Application Insights

Os SDKs do Application Insights para .NET, .NET Core, Java, node. js e JavaScript que todos suportam o rastreio distribuído de forma nativa. Instruções para instalar e configurar cada SDK do Application Insights estão disponíveis abaixo:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

Com o adequada Application Insights SDK instalado e configurado, os informações de rastreamento são automaticamente recolhidas para estruturas populares, bibliotecas e as tecnologias pelo SDK automático-recoletores de dependência. A lista completa de tecnologias suportadas está disponível no [a documentação de coleção automática de dependência](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Além disso, qualquer tecnologia pode ser controlada manualmente com uma chamada para [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) sobre o [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Ativar via OpenCensus

Além de SDKs do Application Insights, o Application Insights suporta também rastreio distribuído por meio [OpenCensus](https://opencensus.io/). OpenCensus é um código-fonte aberto, a desconhecidas do fornecedor, a única distribuição de bibliotecas para fornecer a coleta de métricas e de rastreio distribuído para os serviços. Também permite que a Comunidade de código-fonte aberto ativar o rastreio distribuído com tecnologias populares, como o Redis, Memcached ou MongoDB. [Microsoft colabora no OpenCensus com vários outros parceiros de monitorização e a cloud](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Para adicionar capacidades de rastreio distribuído para uma aplicação com OpenCensus, primeiro [instalar e configurar o reencaminhador de Local do Application Insights](./../../azure-monitor/app/opencensus-local-forwarder.md). A partir daí, configure OpenCensus para encaminhar dados de rastreio distribuído por meio do reencaminhador de Local. Ambos [Python](./../../azure-monitor/app/opencensus-python.md) e [vá](./../../azure-monitor/app/opencensus-go.md) são suportados.

O Web site OpenCensus mantém a documentação de referência de API para [Python](https://opencensus.io/api/python/trace/usage.html) e [vá](https://godoc.org/go.opencensus.io), assim como várias guias diferentes para a utilização de OpenCensus. 

## <a name="next-steps"></a>Passos Seguintes

* [Guia de utilização de OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa da aplicação](./../../azure-monitor/app/app-map.md)
* [Monitorização do desempenho de ponto a ponto](./../../azure-monitor/learn/tutorial-performance.md)
