---
title: O que é a API de Análise de Texto? Técnicas
titleSuffix: Azure Cognitive Services
description: Use a API de Análise de Texto dos serviços cognitivas do Azure para análise de sentimentos, extração de frases-chave, detecção de idioma e reconhecimento de entidade.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 08/26/2019
ms.author: aahi
ms.openlocfilehash: 3b616ac76d2b7fca0d83db2b17b0978bbb249e45
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742717"
---
# <a name="what-is-the-text-analytics-api"></a>O que é a API de Análise de Texto?

O API de Análise de Texto é um serviço baseado em nuvem que fornece processamento de idioma natural avançado sobre texto bruto e inclui quatro funções principais: análise de sentimentos, extração de frases-chave, detecção de idioma e reconhecimento de entidade.

A API faz parte dos [Serviços cognitivas do Azure](https://docs.microsoft.com/azure/cognitive-services/), uma coleção de algoritmos de inteligência de máquina e de ia na nuvem para seus projetos de desenvolvimento.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

A análise de texto pode significar coisas diferentes, mas em serviços cognitivas, o API de Análise de Texto fornece quatro tipos de análise, conforme descrito abaixo. Você pode usar esses recursos com a [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)ou a [biblioteca de cliente](quickstarts/text-analytics-sdk.md).

## <a name="sentiment-analysis"></a>Análise de Sentimentos
Use a [análise de sentimentos](how-tos/text-analytics-how-to-sentiment-analysis.md) para descobrir o que os clientes acham de sua marca ou tópico analisando o texto bruto para obter dicas sobre sentimentos positivos ou negativos. Esta API devolve uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é o mais positivo.<br /> Os modelos de análise são pré-preparados com um corpo extenso de texto e tecnologias de linguagem natural da Microsoft. Para os [idiomas selecionados](text-analytics-supported-languages.md), a API pode analisar e classificar qualquer texto não processado que fornecer, devolvendo diretamente os resultados à aplicação de chamada.

## <a name="key-phrase-extraction"></a>Extração de Expressões-Chave
[Extraia automaticamente frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md) para identificar rapidamente os pontos principais. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”.

## <a name="language-detection"></a>Deteção de Idioma
Você pode [detectar em qual idioma o texto de entrada é gravado](how-tos/text-analytics-how-to-language-detection.md) e relatar um único código de idioma para cada documento enviado na solicitação em uma grande variedade de idiomas, variantes, dialetos e alguns idiomas regionais/culturais. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação.

## <a name="named-entity-recognition"></a>Reconhecimento de Entidades Nomeadas
[Identificar e categorizar entidades](how-tos/text-analytics-how-to-entity-linking.md) em seu texto como pessoas, lugares, organizações, data/hora, quantidades, percentuais, moedas e muito mais. As entidades conhecidas também são reconhecidas e ligadas a mais informações na Web.

## <a name="use-containers"></a>Utilizar contentores

[Use os contêineres de análise de texto](how-tos/text-analytics-how-to-install-containers.md) para extrair frases-chave, detectar linguagem e analisar o sentimentos localmente, instalando contêineres do Docker padronizados mais perto de seus dados.

## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: submete dados para análise e processa as saídas no seu código. Os analisadores são consumidos tal como estão, sem configuração ou personalização adicionais.

1. [Crie um recurso do Azure](../cognitive-services-apis-create-account.md) para análise de texto. Posteriormente, [obtenha a chave](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) gerada para autenticar suas solicitações.

2. [Formule um pedido](how-tos/text-analytics-how-to-call-api.md#json-schema) que contém os dados como texto não estruturado e não processado, em JSON.

3. Publique o pedido no ponto final estabelecido durante a inscrição, anexando o recurso pretendido: análise de sentimentos, extração de expressões-chave, deteção de idioma ou identificação de entidade.

4. Transmita a resposta em fluxo ou armazene-a localmente. Dependendo do pedido, os resultados são uma pontuação de sentimento, uma coleção de expressões-chave extraídas ou um código de idioma.

A saída é devolvida como um único documento JSON, com resultados para cada documento de texto que publicou, com base no ID. Posteriormente, pode analisar, visualizar ou categorizar os resultados em informações acionáveis.

Os dados não são armazenados na sua conta. As operações realizadas pela API de Análise de Texto não têm um estado específico, o que significa que o texto que fornecer é processado e os resultados são devolvidos imediatamente.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Análise de Texto para vários níveis de experiência de programação

Você pode começar a usar o API de Análise de Texto em seus processos, mesmo que você não tenha muita experiência em programação. Use estes tutoriais para saber como você pode usar a API para analisar o texto de diferentes maneiras de se adequar ao seu nível de experiência. 

* Programação mínima necessária:
    * [Use o API de Análise de Texto e o MS Flow para identificar a opinião dos comentários em um grupo do Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integre Power BI com o API de Análise de Texto para analisar os comentários do cliente](tutorials/tutorial-power-bi-key-phrases.md)
* Experiência de programação recomendada:
    * [Análise de sentimentos sobre streaming de dados usando Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Crie um aplicativo Flask para traduzir texto, analisar sentimentos e sintetizar a fala](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Linguagens suportadas

Esta secção foi movida para um artigo separado para melhor deteção. Consulte [os idiomas com suporte no API de análise de texto](text-analytics-supported-languages.md) para este conteúdo.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de dados

Todos os pontos finais da API de Análise de Texto aceitam dados de texto não processados. O limite atual é de 5.120 caracteres para cada documento; Se você precisar analisar documentos maiores, poderá dividi-los em partes menores.

| Limite | Valor |
|------------------------|---------------|
| Tamanho máximo de um documento único | 5\.120 caracteres conforme medido por [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Tamanho máximo do pedido completo | 1 MB |
| Número máximo de documentos num pedido | 1000 documentos |

Seu limite de taxa variará com o tipo de preço.

| Escalão          | Solicitações por segundo | Solicitações por minuto |
|---------------|---------------------|---------------------|
| S/multi-serviço | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

As solicitações são medidas para cada recurso de Análise de Texto separadamente. Por exemplo, você pode enviar o número máximo de solicitações de seu tipo de preço para cada recurso, ao mesmo tempo.      

## <a name="unicode-encoding"></a>Codificação Unicode

A API de Análise de Texto utiliza a codificação Unicode para representação de texto e cálculos de contagem de carateres. Os pedidos podem ser submetidos em UTF-8 e UTF-16, sem diferenças mensuráveis na contagem de carateres. Os pontos de código Unicode são utilizados como a heurística para o comprimento dos carateres, e são considerados equivalentes para efeitos de limites de dados de análise de texto. Se você usar [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) para obter a contagem de caracteres, você está usando o mesmo método que usamos para medir o tamanho dos dados.

## <a name="next-steps"></a>Passos seguintes

+ [Crie um recurso do Azure](../cognitive-services-apis-create-account.md) para análise de texto para obter uma chave e um ponto de extremidade para seus aplicativos.

+ O [Início Rápido](quickstarts/text-analytics-sdk.md) é uma descrição das chamadas à API REST escritas em C#. Saiba como submeter texto, escolher uma análise e ver os resultados com pouco código. Se preferir, você pode começar com o início [rápido do Python](quickstarts/python.md) em vez disso.

+ Veja [o que há de novo na API de análise de texto](whats-new.md) para obter informações sobre novas versões e recursos.

+ Mergulhe em um pouco mais fundo com este [tutorial de análise de sentimentos](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) usando Azure Databricks.

+ Confira nossa lista de postagens de blog e mais vídeos sobre como usar o API de Análise de Texto com outras ferramentas e tecnologias em nossa [página de conteúdo de & comunidade externa](text-analytics-resource-external-community.md).
