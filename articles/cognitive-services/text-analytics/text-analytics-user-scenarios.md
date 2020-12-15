---
title: Exemplo de cenários de utilizador para a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para ver alguns cenários comuns para integrar a API text Analytics nos seus serviços e processos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: a4c551a6e9b8d79fdeb7b5219f311c7c7c969757
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505363"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Exemplo de cenários de utilizador para a API de Análise de Texto

A API Text Analytics é um serviço baseado na nuvem que fornece processamento avançado de linguagem natural sobre texto. Este artigo descreve alguns casos de uso de exemplo para integrar a API nas suas soluções e processos empresariais. 

## <a name="analyze-survey-results"></a>Analisar os resultados do inquérito

Desenhe informações dos resultados do inquérito ao cliente e ao empregado, processando as respostas de texto bruto utilizando a Análise do Sentimento. Agregar os resultados para análise, acompanhamento e compromissos de condução.

![Uma imagem descrevendo como realizar análises de sentimentos em inquéritos de clientes e colaboradores.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analise as chamadas de clientes de entrada gravadas

Extrair informações das chamadas de serviços ao cliente utilizando a análise de discurso a texto, a análise do sentimento e a extração de frases-chave. Exiba os resultados no painel Power BI ou num portal para entender melhor os clientes, destacar as tendências de atendimento ao cliente e impulsionar o envolvimento do cliente. Envie pedidos de API como um lote para reportagem, ou em tempo real para intervenção. Consulte o código de amostra [no GitHub.](https://github.com/rlagh2/callcenteranalytics)

![Uma imagem descrevendo como automatizar informações de chamadas de atendimento ao cliente usando análise de sentimento](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Processo e categorização incidentes de apoio

Utilize o Reconhecimento de Extração de Frase-Chave e reconhecimento de entidades para processar pedidos de suporte submetidos em formato textual não estruturado. Utilize as frases e entidades extraídas para categorizar os pedidos de planeamento de recursos e análise de tendências.

![Uma imagem descrevendo como usar a extração de frases-chave e o reconhecimento de entidades para categorizar relatórios e tendências de incidentes](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorize as redes sociais do seu produto

Monitorize o feedback do produto do utilizador no twitter ou na página de Facebook do seu produto. Utilize os dados para analisar o sentimento do cliente em relação aos lançamentos de novos produtos, extrair frases-chave sobre funcionalidades e pedidos de funcionalidades ou abordar as reclamações dos clientes à medida que estes ocorram. Veja o exemplo do [modelo de automatismo de energia da Microsoft.](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/)

![Uma imagem descrevendo como monitorizar o feedback do seu produto e da empresa nas redes sociais usando a extração de frases-chave](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Classificar e redigir documentos que tenham informações sensíveis

Utilizar o Reconhecimento de Entidade Nomeada para identificar informações pessoais e sensíveis em documentos. Utilize os dados para classificar documentos ou redirecioná-los para que possam ser partilhados com segurança.

![Uma imagem descrevendo como usar o NER para detetar informações pessoais e classificar e redigir documentos](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>Realizar mineração de opinião

Opiniões de grupo relacionadas com aspetos específicos de um produto ou serviço em inquéritos, feedback do cliente ou onde quer que o texto tenha uma opinião sobre um aspeto. Use-o para ajudar a orientar lançamentos e melhorias de produtos, esforços de marketing ou realçar o desempenho do seu produto ou serviço. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="Exemplo de opiniões sobre um hotel.":::

## <a name="next-steps"></a>Passos seguintes

* [O que é a API de Análise de Texto?](overview.md)
* [Envie um pedido à API text Analytics usando a biblioteca do cliente](quickstarts/client-libraries-rest-api.md)
