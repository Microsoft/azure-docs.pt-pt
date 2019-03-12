---
title: Descrição geral da Web Language Model API
titleSuffix: Azure Cognitive Services
description: A Web Language Model API nos Serviços Cognitivos da Microsoft fornece ferramentas topo de gama para processamento de linguagem natural.
services: cognitive-services
author: piyushbehre
manager: nitinme
ms.service: cognitive-services
ms.subservice: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ROBOTS: NOINDEX
ms.openlocfilehash: 751b6e5979174eae9bc815061f16bf99bd0899cf
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534791"
---
# <a name="what-is-the-web-language-model-api-preview"></a>O que é a Web Language Model API? (Pré-visualização)

> [!IMPORTANT]
> A pré-visualização do Web Language Model foi desativada a 9 de agosto de 2018. Recomendamos que utilize os [módulos de análise de texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para o processamento e a análise de texto.

A Microsoft Web Language Model API é um serviço cloud baseado em REST que fornece ferramentas topo de gama para processamento de linguagem natural. Com esta API, a sua aplicação pode tirar partido do poder dos macrodados através de modelos de linguagem preparados com base no corpora à escala da Web recolhido pelo Bing no mercado en-US.

Estes modelos de linguagem de grama-N de backoff suavizados, que suportam cadeias de Markov até à 5ª ordem, são preparados no seguinte corpora:

- Texto do corpo da página Web
- Texto do título da página Web
- Texto da âncora da página Web
- Texto da consulta da pesquisa Web

A Web Language Model API suporta quatro operações de pesquisa:

1. Probabilidade conjunta (log10) de uma sequência de palavras.
2. Probabilidade condicional (log10) de uma palavra numa sequência de palavras anteriores.
3. Lista de palavras (conclusões) com maior probabilidade de concluir uma sequência de palavras.
4. Separação de palavras de cadeias de carateres sem espaços.

## <a name="getting-started"></a>Introdução

1. Subscreva o serviço.
2. Transfira o [SDK](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Execute o código de exemplo do SDK.
4. Veja a [Referência da API](https://web.archive.org/web/20170503191852/westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51) para obter detalhes completos sobre os pontos finais, incluindo fragmentos de código em várias linguagens.

## <a name="underlying-technology"></a>Tecnologia Subjacente

O documento seguinte fornece detalhes sobre o desenvolvimento destes modelos de linguagem e deve ser citado nas publicações de investigação que utilizam este serviço:

- [Descrição geral do Microsoft Web N-gram Corpus e Aplicações](https://research.microsoft.com/apps/pubs/default.aspx?id=130762), NAACL-HLT 2010

Clique [aqui](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0) para obter uma lista atualizada de documentos que citam este trabalho.
