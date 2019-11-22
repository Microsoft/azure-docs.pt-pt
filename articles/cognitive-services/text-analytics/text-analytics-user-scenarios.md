---
title: Cenários de usuário de exemplo para o API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Use este artigo para ver alguns cenários comuns de integração do API de Análise de Texto aos seus serviços e processos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: f11d23207a21af5c73831d0edd5ffc9eaa59c119
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280663"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Cenários de usuário de exemplo para o API de Análise de Texto

O API de Análise de Texto é um serviço baseado em nuvem que fornece processamento de idioma natural avançado sobre o texto. Este artigo descreve alguns casos de uso de exemplo para integrar a API em seus processos e soluções de negócios. 

## <a name="analyze-survey-results"></a>Analisar resultados da pesquisa

Desenhe informações de resultados de pesquisa de clientes e funcionários processando as respostas de texto bruto usando Análise de Sentimento. Agregue as descobertas para análise, acompanhamento e condução de compromissos.

![Uma imagem que descreve como executar a análise de sentimentos em pesquisas de clientes e funcionários.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analisar as chamadas de cliente de entrada gravadas

Extraia informações de chamadas de serviços de clientes usando Conversão de Texto em Fala, Análise de Sentimento e Extração de Frases-chave. Exiba os resultados no painel Power BI ou em um portal para entender melhor os clientes, destaque as tendências do serviço de atendimento ao cliente e conduza o envolvimento do cliente. Envie solicitações de API como um lote para relatórios ou em tempo real para intervenção. Consulte o [código de exemplo](https://github.com/rlagh2/callcenteranalytics).

![Uma imagem que descreve como automatizar a obtenção de informações de chamadas de atendimento ao cliente usando análise de sentimentos](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Processar e categorizar incidentes de suporte

Use Extração de Frases-chave e o reconhecimento de entidade para processar solicitações de suporte enviadas em formato textual não estruturado. Use as frases e entidades extraídas para categorizar as solicitações de planejamento de recursos e análise de tendências.

![Uma imagem que descreve como usar extração de frases-chave e reconhecimento de entidade para categorizar relatórios de incidentes e tendências](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorar os feeds de mídia social de seu produto

Monitore os comentários sobre o produto do usuário na página do Twitter ou do Facebook do seu produto. Use os dados para analisar os sentimentos do cliente em direção a lançamentos de novos produtos, extrair frases-chave sobre recursos e solicitações de recurso ou resolver reclamações de clientes à medida que elas acontecem. Consulte o [modelo de Microsoft Flow](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/)de exemplo.

![Uma imagem que descreve como monitorar seus comentários de produto e empresa na mídia social usando a extração de frases-chave](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Passos seguintes

* [O que é o API de Análise de Texto?](overview.md)
* [Enviar uma solicitação para o API de Análise de Texto usando a biblioteca de cliente](quickstarts/text-analytics-sdk.md)
