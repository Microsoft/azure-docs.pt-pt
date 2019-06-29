---
title: Cenários de utilizador de exemplo para a API de análise de texto
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para ver alguns cenários comuns para integrar a API de análise de texto em seus processos e serviços.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: d8d2aed16b5af60c44501e6d72332783a164ff91
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478395"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Cenários de utilizador de exemplo para a API de análise de texto

A API de análise de texto é um serviço baseado na nuvem que fornece processamento avançado de linguagem natural relativamente a texto. Este artigo descreve alguns casos de utilização de exemplo para integrar a API em seus processos e soluções de negócios. 

## <a name="analyze-survey-results"></a>Analisar os resultados de pesquisa

Obtenha informações a partir dos clientes e funcionários resultados de pesquisa ao processar as respostas de texto não processado através da análise de sentimentos. Agregar as descobertas para análise, acompanhamento e impulsionando o envolvimento com o.

![Uma imagem que descreve como executar a análise de sentimentos em pesquisas de clientes e funcionários.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analisar chamadas dos clientes de entrada gravado

Extraia informações de chamadas de serviços de cliente com o texto em voz, análise de sentimentos e extração de expressões chave. Exiba os resultados no dashboard do Power BI ou um portal para melhor compreender os clientes, realçar tendências de serviço de cliente e envolvimento do cliente de unidade. Envie pedidos da API como um lote para relatórios ou em tempo real de intervenção. Ver [código de exemplo](https://github.com/rlagh2/callcenteranalytics).

![Uma imagem que descreve como automatizar a obtenção de informações de chamadas de serviço de cliente através da análise de sentimentos](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Processar e categorizar os incidentes de suporte

Utilize a extração de expressões chave e o reconhecimento de entidades para processar pedidos de suporte submetidos no formato de texto não estruturado. Utilize as frases extraídos e entidades para categorizar os pedidos de análise de tendências e planejamento de recursos.

![Uma imagem que descreve como utilizar o reconhecimento de extração e a entidade de expressões-chave para categorizar e tendências de relatórios de incidentes](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorizar os feeds de redes sociais de seu produto

Monitorizar os comentários de produto do usuário no twitter ou a página do Facebook do seu produto. Utilize os dados para analisar o sentimento dos clientes no sentido de novos lançamentos de produtos, extrair expressões-chave sobre funcionalidades e pedidos de funcionalidades ou reclamações de cliente de endereço, à medida que acontecem.

![Uma imagem que descreve como monitorizar o seu feedback do produto e da empresa nas redes sociais, extração de expressões-chave a utilizar](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Passos Seguintes

* [O que é a API de análise de texto?](overview.md)
* [Enviar um pedido para utilizar a API de análise de textoC#](quickstarts/csharp.md)
