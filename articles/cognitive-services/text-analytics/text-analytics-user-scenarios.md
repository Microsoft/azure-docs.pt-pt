---
title: Exemplo de cenários de utilizador para a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para ver alguns cenários comuns para integrar a API de Análise de Texto nos seus serviços e processos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 02/25/2020
ms.author: aahi
ms.openlocfilehash: 560f34d21a68cecc4950db5dcceb9c8ef7b05a49
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614440"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Exemplo de cenários de utilizador para a API de Análise de Texto

O Text Analytics API é um serviço baseado na nuvem que fornece processamento avançado de linguagem natural através de texto. Este artigo descreve alguns casos de utilização de exemplo para integrar a API nas suas soluções e processos de negócio. 

## <a name="analyze-survey-results"></a>Analisar os resultados do Inquérito

Desenhe informações dos resultados do inquérito ao cliente e dos colaboradores processando as respostas de texto bruto utilizando a Sentiment Analysis. Agregar os resultados para análise, acompanhamento e compromissos de condução.

![Uma imagem que descreve como realizar análises de sentimentos em inquéritos de clientes e funcionários.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analisar chamadas de clientes gravadas

Extrair informações das chamadas de serviços ao cliente utilizando texto para a fala, análise de sentimento e extração de frases-chave. Exiba os resultados no dashboard Power BI ou num portal para melhor entender os clientes, destacar as tendências de atendimento ao cliente e impulsionar o envolvimento do cliente. Envie pedidos de API como um lote para reporte, ou em tempo real para intervenção. Consulte o código da amostra [no GitHub](https://github.com/rlagh2/callcenteranalytics).

![Uma imagem descrevendo como automatizar obter insights de chamadas de atendimento ao cliente usando análise de sentimento](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Processar e categorizar incidentes de apoio

Utilize a extração de frases-chave e o reconhecimento da entidade para processar pedidos de suporte apresentados em formato textual não estruturado. Utilize as frases e entidades extraídas para categorizar os pedidos de planeamento de recursos e análise de tendências.

![Uma imagem descrevendo como usar a extração de frases-chave e reconhecimento de entidade para categorizar relatórios e tendências de incidentes](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorize os feeds das redes sociais do seu produto

Monitorize o feedback do utilizador na página de Twitter ou Facebook do seu produto. Utilize os dados para analisar o sentimento do cliente em relação aos lançamentos de novos produtos, extrair frases-chave sobre funcionalidades e pedidos de funcionalidades, ou abordar as reclamações dos clientes à medida que estes acontecerem. Veja o exemplo [do modelo de Fluxo](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/)do Microsoft .

![Uma imagem descrevendo como monitorizar o feedback do seu produto e empresa nas redes sociais usando a extração de frases-chave](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Classificar e redigir documentos que tenham informações sensíveis

Utilizar o Reconhecimento de Entidades Nomeadas para identificar informações pessoais e sensíveis em documentos. Utilize os dados para classificar documentos ou redigi-los para que possam ser partilhados com segurança.

![Uma imagem descrevendo como usar ner para detetar informações pessoais e classificar e redigir documentos](media/use-cases/sensitive-docs.jpg)

## <a name="next-steps"></a>Passos seguintes

* [O que é a API de Análise de Texto?](overview.md)
* [Envie um pedido para a API de Análise de Texto utilizando a biblioteca do cliente](quickstarts/text-analytics-sdk.md)
