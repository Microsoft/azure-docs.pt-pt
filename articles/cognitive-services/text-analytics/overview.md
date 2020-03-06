---
title: O que é a API de Análise de Texto? - Capacidades -
titleSuffix: Azure Cognitive Services
description: Utilize a API de Análise de Texto dos Serviços Cognitivos Azure para análise de sentimentos, extração de frases-chave, deteção de linguagem e reconhecimento de entidades.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: a9519be591581fa434825f1a1fb31749788a21a8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395715"
---
# <a name="what-is-the-text-analytics-api"></a>O que é a API de Análise de Texto?

O Text Analytics API é um serviço baseado na nuvem que fornece processamento avançado de linguagem natural sobre texto cru, e inclui quatro funções principais: análise de sentimentos, extração de frases-chave, deteção de linguagem e reconhecimento de entidades nomeadas.

A API faz parte dos [Serviços Cognitivos Azure,](https://docs.microsoft.com/azure/cognitive-services/)uma coleção de machine learning e algoritmos de IA na nuvem para os seus projetos de desenvolvimento.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

A análise de texto pode significar coisas diferentes, mas nos Serviços Cognitivos, a API text analytics fornece quatro tipos de análise, conforme descrito abaixo. Pode utilizar estas funcionalidades com a [API REST,](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)ou a biblioteca de [clientes.](quickstarts/text-analytics-sdk.md)

## <a name="sentiment-analysis"></a>Análise de Sentimentos
Use a análise de [sentimentos](how-tos/text-analytics-how-to-sentiment-analysis.md) para descobrir o que os clientes pensam da sua marca ou tópico analisando texto cru para pistas sobre sentimentos positivos ou negativos. Esta API devolve uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é o mais positivo.<br /> Os modelos de análise são pré-preparados com um corpo extenso de texto e tecnologias de linguagem natural da Microsoft. Para os [idiomas selecionados](text-analytics-supported-languages.md), a API pode analisar e classificar qualquer texto não processado que fornecer, devolvendo diretamente os resultados à aplicação de chamada.

## <a name="key-phrase-extraction"></a>Extração de Expressões-Chave
[Extraiautomaticamente frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md) para identificar rapidamente os pontos principais. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”.

## <a name="language-detection"></a>Deteção de Idioma
Pode [detetar em que língua o texto](how-tos/text-analytics-how-to-language-detection.md) de entrada está escrito e reportar um único código linguístico para cada documento submetido a pedido numa vasta gama de línguas, variantes, dialetos e algumas línguas regionais/culturais. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação.

## <a name="named-entity-recognition"></a>Reconhecimento de Entidades Nomeadas
[Identifique e categorize entidades](how-tos/text-analytics-how-to-entity-linking.md) no seu texto como pessoas, lugares, organizações, data/hora, quantidades, percentagens, moedas e muito mais. As entidades conhecidas também são reconhecidas e ligadas a mais informações na Web.

## <a name="use-containers"></a>Utilizar contentores

[Utilize os recipientes Text Analytics](how-tos/text-analytics-how-to-install-containers.md) para extrair frases-chave, detetar linguagem e analisar o sentimento localmente, instalando recipientes Docker padronizados mais próximos dos seus dados.

## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: submete dados para análise e processa as saídas no seu código. Os analisadores são consumidos tal como estão, sem configuração ou personalização adicionais.

1. [Crie um recurso Azure](../cognitive-services-apis-create-account.md) para Análise de Texto. Depois, [obtenha a chave](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) gerada para autenticar os seus pedidos.

2. [Formule um pedido](how-tos/text-analytics-how-to-call-api.md#json-schema) que contém os dados como texto não estruturado e não processado, em JSON.

3. Publique o pedido ao ponto final estabelecido durante a inscrição, anexando o recurso desejado: análise de sentimento, extração de frases-chave, deteção de linguagem ou reconhecimento de entidades nomeadas.

4. Transmita a resposta em fluxo ou armazene-a localmente. Dependendo do pedido, os resultados são uma pontuação de sentimento, uma coleção de expressões-chave extraídas ou um código de idioma.

A saída é devolvida como um único documento JSON, com resultados para cada documento de texto que publicou, com base no ID. Posteriormente, pode analisar, visualizar ou categorizar os resultados em informações acionáveis.

Os dados não são armazenados na sua conta. As operações realizadas pela API de Análise de Texto não têm um estado específico, o que significa que o texto que fornecer é processado e os resultados são devolvidos imediatamente.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Análise de texto para vários níveis de experiência de programação

Pode começar a utilizar a API textanalytics nos seus processos, mesmo que não tenha muita experiência em programação. Use estes tutoriais para aprender como pode usar a API para analisar o texto de diferentes formas para se adaptar ao seu nível de experiência. 

* Programação mínima necessária:
    * [Extrair informações em Excel usando Análise de Texto e Automatização de Energia](tutorials/extract-excel-information.md)
    * [Utilize o API de Análise de Texto e MS Flow para identificar o sentimento de comentários num grupo Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrar o Power BI com a API de Análise de Texto para analisar o feedback do cliente](tutorials/tutorial-power-bi-key-phrases.md)
* Experiência de programação recomendada:
    * [Análise de sentimento sobre dados de streaming usando Tijolos de Dados Azure](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Construa uma aplicação do Flask para traduzir texto, analisar o sentimento e sintetizar o discurso](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Linguagens suportadas

Esta secção foi movida para um artigo separado para melhor deteção. Consulte [as línguas suportadas na API de Análise de Texto](text-analytics-supported-languages.md) para este conteúdo.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de dados

Todos os pontos finais da API de Análise de Texto aceitam dados de texto não processados. O limite atual é de 5.120 caracteres para cada documento; se precisar de analisar documentos maiores, pode separá-los em pedaços menores.

| Limite | Valor |
|------------------------|---------------|
| Tamanho máximo de um documento único | 5\.120 caracteres medidos por [`StringInfo.LengthInTextElements`. ](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) |
| Tamanho máximo do pedido completo | 1 MB |
| Número máximo de documentos num pedido | 1000 documentos |

O seu limite de tarifas variará com o seu nível de preços.

| Escalão          | Pedidos por segundo | Pedidos por minuto |
|---------------|---------------------|---------------------|
| S / Multi-serviço | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Os pedidos são medidos para cada função de Análise de Texto separadamente. Por exemplo, pode enviar o número máximo de pedidos para o seu nível de preços para cada funcionalidade, ao mesmo tempo.      

## <a name="unicode-encoding"></a>Codificação Unicode

A API de Análise de Texto utiliza a codificação Unicode para representação de texto e cálculos de contagem de carateres. Os pedidos podem ser submetidos em UTF-8 e UTF-16, sem diferenças mensuráveis na contagem de carateres. Os pontos de código Unicode são utilizados como a heurística para o comprimento dos carateres, e são considerados equivalentes para efeitos de limites de dados de análise de texto. Se utilizar [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) para obter a contagem de caracteres, está a usar o mesmo método que usamos para medir o tamanho dos dados.

## <a name="next-steps"></a>Passos seguintes

+ [Crie um recurso Azure](../cognitive-services-apis-create-account.md) para o Text Analytics para obter uma chave e ponto final para as suas aplicações.

+ Use o [quickstart](quickstarts/text-analytics-sdk.md) para começar a enviar chamadas API. Saiba como submeter texto, escolher uma análise e ver os resultados com pouco código.

+ Veja [as novidades na API](whats-new.md) de Análise de Texto para obter informações sobre novos lançamentos e funcionalidades.

+ Investigue um pouco mais fundo com este tutorial de análise de [sentimentousando](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) Os Tijolos de Dados Azure.

+ Confira a nossa lista de posts de blog e mais vídeos sobre como usar a API text analytics com outras ferramentas e tecnologias na nossa página de [Conteúdo Externo e Comunitário.](text-analytics-resource-external-community.md)
