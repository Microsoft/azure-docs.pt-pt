---
title: Mineração de texto e análise com a API text Analytics - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a mineração de texto com a API text Analytics. Use-o para análise de sentimentos, deteção de linguagem e outras formas de processamento de linguagem natural.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/02/2020
ms.author: aahi
keywords: mineração de texto, análise de sentimento, análise de texto
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d58c501af3d90fec1eea43d13fa2383c8e847f18
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289702"
---
# <a name="what-is-the-text-analytics-api"></a>O que é a API de Análise de Texto?

A API text Analytics é um serviço baseado na nuvem que fornece funcionalidades de processamento de linguagem natural (NLP) para a mineração de texto e análise de texto, incluindo: análise de sentimentos, mineração de opinião, extração de frases-chave, deteção de linguagem e reconhecimento de entidades nomeadas.

A API faz parte da [Azure Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/)uma coleção de machine learning e algoritmos de IA na nuvem para os seus projetos de desenvolvimento. Pode utilizar estas funcionalidades com a [API REST,](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)ou com a biblioteca do [cliente.](quickstarts/text-analytics-sdk.md)

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

## <a name="sentiment-analysis"></a>Análise de sentimentos

Use a análise de [sentimentos](how-tos/text-analytics-how-to-sentiment-analysis.md) e descubra o que as pessoas pensam da sua marca ou tópico, minerando o texto para obter pistas sobre sentimentos positivos ou negativos. 

A funcionalidade fornece rótulos de sentimento (tais como "negativo", "neutro" e "positivo") com base na pontuação de confiança mais elevada encontrada pelo serviço a nível de uma frase e de documento. Esta funcionalidade também devolve pontuações de confiança entre 0 e 1 por cada documento & frases dentro do mesmo por sentimento positivo, neutro e negativo. Também pode ser executado o serviço nas instalações [utilizando um recipiente.](how-tos/text-analytics-how-to-install-containers.md)

A partir da pré-visualização v3.1, a mineração de opinião é uma característica da Análise do Sentimento. Também conhecida como Análise de Sentimento baseada em Aspetos no Processamento de Linguagem Natural (NLP), esta funcionalidade fornece mais informações granulares sobre as opiniões relacionadas com aspetos (como os atributos de produtos ou serviços) em texto.

## <a name="key-phrase-extraction"></a>Extração de expressões-chave

Utilize a [extração de frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md) para identificar rapidamente os principais conceitos em texto. Por exemplo, no texto "A comida era deliciosa e havia uma equipa maravilhosa", a Chave Desvaição dos principais pontos de conversa: "comida" e "pessoal maravilhoso".

## <a name="language-detection"></a>Deteção de idioma

A deteção de [idiomas](how-tos/text-analytics-how-to-language-detection.md) pode detetar a linguagem em que um texto de entrada é escrito e reportar um único código linguístico para cada documento submetido a pedido em uma ampla gama de línguas, variantes, dialetos e algumas línguas regionais/culturais. O código linguístico é emparelhado com uma pontuação de confiança.

## <a name="named-entity-recognition"></a>Reconhecimento de entidades nomeadas

O Nome De Reconhecimento de Entidade (NER) pode [identificar e categorizar entidades](how-tos/text-analytics-how-to-entity-linking.md) no seu texto como pessoas, locais, organizações, quantidades, entidades conhecidas também são reconhecidas e ligadas a mais informações na web.

## <a name="use-containers"></a>Utilizar contentores

[Utilize os recipientes Text Analytics](how-tos/text-analytics-how-to-install-containers.md) como solução no local para o texto de mineração e para a utilização da API. Estes recipientes Docker permitem-lhe extrair frases-chave, detetar linguagem e analisar o sentimento mais próximo dos seus dados.

## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: submete dados para análise e processa as saídas no seu código. Os analisadores são consumidos tal como estão, sem configuração ou personalização adicionais.

1. [Crie um recurso Azure](../cognitive-services-apis-create-account.md) para Análise de Texto. Em seguida, [obtenha a chave](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) gerada para que você autuça os seus pedidos.

2. [Formule um pedido](how-tos/text-analytics-how-to-call-api.md#json-schema) que contém os dados como texto não estruturado e não processado, em JSON.

3. Publicar o pedido no ponto final estabelecido durante a inscrição, anexando o recurso pretendido: análise de sentimento, extração de frases-chave, deteção de linguagem ou reconhecimento de entidade nomeada.

4. Transmita a resposta em fluxo ou armazene-a localmente. Dependendo do pedido, os resultados são uma pontuação de sentimento, uma coleção de expressões-chave extraídas ou um código de idioma.

A saída é devolvida como um único documento JSON, com resultados para cada documento de texto que publicou, com base no ID. Posteriormente, pode analisar, visualizar ou categorizar os resultados em informações acionáveis.

Os dados não são armazenados na sua conta. As operações realizadas pela API de Análise de Texto não têm um estado específico, o que significa que o texto que fornecer é processado e os resultados são devolvidos imediatamente.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Análise de texto para vários níveis de experiência de programação

Pode começar a utilizar a API text Analytics nos seus processos, mesmo que não tenha muita experiência em programação. Use estes tutoriais para aprender como pode usar a API para analisar texto de diferentes formas para se adaptar ao seu nível de experiência. 

* Programação mínima necessária:
    * [Extrair informações no Excel usando Análise de Texto e Automatizar Energia](tutorials/extract-excel-information.md)
    * [Utilize a API de Análise de Texto e o MS Flow para identificar o sentimento dos comentários num grupo Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integre o Power BI com a API de Análise de Texto para analisar o feedback do cliente](tutorials/tutorial-power-bi-key-phrases.md)
* Experiência de programação recomendada:
    * [Análise de sentimentos dos dados de transmissão em fluxo com o Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Construa uma app Flask para traduzir texto, analisar sentimentos e sintetizar a fala](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Linguagens suportadas

Esta secção foi movida para um artigo separado para melhor deteção. Consulte [os idiomas suportados na API text Analytics](text-analytics-supported-languages.md) para este conteúdo.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de dados

Todos os pontos finais da API de Análise de Texto aceitam dados de texto não processados. Consulte o artigo [Data limits](concepts/data-limits.md) para obter mais informações.

## <a name="unicode-encoding"></a>Codificação Unicode

A API de Análise de Texto utiliza a codificação Unicode para representação de texto e cálculos de contagem de carateres. Os pedidos podem ser submetidos em UTF-8 e UTF-16, sem diferenças mensuráveis na contagem de carateres. Os pontos de código Unicode são utilizados como a heurística para o comprimento dos carateres, e são considerados equivalentes para efeitos de limites de dados de análise de texto. Se utilizar [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) para obter a contagem de caracteres, está a usar o mesmo método que usamos para medir o tamanho dos dados.

## <a name="next-steps"></a>Passos seguintes

+ [Crie um recurso Azure](../cognitive-services-apis-create-account.md) para text Analytics para obter uma chave e ponto final para as suas aplicações.

+ Use o [quickstart](quickstarts/text-analytics-sdk.md) para começar a enviar chamadas API. Saiba como submeter texto, escolher uma análise e ver os resultados com pouco código.

+ Veja [as novidades na API text Analytics](whats-new.md) para obter informações sobre novas versões e funcionalidades.

+ Investigue um pouco mais com este tutorial de [análise de sentimento](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) usando Azure Databricks.

+ Consulte a nossa lista de posts de blog e mais vídeos sobre como usar a API text Analytics com outras ferramentas e tecnologias na nossa [página de Conteúdo Comunitário de & Externos.](text-analytics-resource-external-community.md)
