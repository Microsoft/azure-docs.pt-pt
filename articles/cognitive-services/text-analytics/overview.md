---
title: O que é a Análise de Texto?
titleSuffix: Azure Cognitive Services
description: Análise de Texto nos Serviços Cognitivos do Azure para análise de sentimentos, extração de expressões-chave, deteção de idioma e associação de entidades.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 7623f98b9fd6c4bad8a41050e39b6e0e8650dccc
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889293"
---
# <a name="what-is-text-analytics"></a>O que é a Análise de Texto?

A API de Análise de Texto é um serviço com base na cloud que fornece capacidades de processamento avançado de linguagem natural em texto não processado. Inclui quatro funções principais: análise de sentimentos, extração de expressões-chave, deteção de idioma e associação de entidades.

A API é uma parte [serviços cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/), uma coleção de machine learning e algoritmos de IA na cloud para seus projetos de desenvolvimento.

## <a name="capabilities-in-text-analytics"></a>Funções na Análise de Texto

A análise de texto pode ter diferentes significados mas, nos Serviços Cognitivos, a API de Análise de Texto fornece quatro tipos de análise, conforme descrito na tabela seguinte.

| Operações| Descrição | APIs |
|-----------|-------------|------|
|[**Análise de Sentimentos**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Descubra o que os clientes pensam da sua marca ou tópico, através da análise de texto não processado para obter sugestões sobre o sentimento positivo ou negativo. Esta API devolve uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é o mais positivo.<br /> Os modelos de análise são pré-preparados com um corpo extenso de texto e tecnologias de linguagem natural da Microsoft. Para os [idiomas selecionados](text-analytics-supported-languages.md), a API pode analisar e classificar qualquer texto não processado que fornecer, devolvendo diretamente os resultados à aplicação de chamada. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Extração de Expressões-Chave**](how-tos/text-analytics-how-to-keyword-extraction.md) | Extraia expressões-chave automaticamente para identificar rapidamente os pontos principais. Por exemplo, para o texto de entrada “The food was delicious and there were wonderful staff”, a API devolve os pontos de conversa principais: “food” e “wonderful staff”.  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Deteção de Idioma**](how-tos/text-analytics-how-to-language-detection.md) | Para até 120 idiomas, detete o idioma no qual o texto de entrada foi escrito e reporte um único código de idioma para cada documento submetido no pedido. O código de idioma é emparelhado com uma pontuação que indica a força da pontuação. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Reconhecimento de Entidades (Pré-visualização)**](how-tos/text-analytics-how-to-entity-linking.md) | Identifique e categorize entidades no seu texto, como pessoas, locais, organizações, data/hora, quantidades, percentagens, moedas e muito mais. As entidades conhecidas também são reconhecidas e ligadas a mais informações na Web. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) |

## <a name="use-containers"></a>Utilizar contentores

[Utilize os contentores de análise de texto](how-tos/text-analytics-how-to-install-containers.md) para extrair expressões-chave, detetar o idioma e analisar sentimentos localmente, mediante a instalação de contentores do Docker padronizados mais próximos aos seus dados.

## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: submete dados para análise e processa as saídas no seu código. Os analisadores são consumidos tal como estão, sem configuração ou personalização adicionais.

1. [Inscreva-se](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para obter uma [chave de acesso](how-tos/text-analytics-how-to-access-key.md). A chave tem de ser transmitida em cada pedido.

2. [Formule um pedido](how-tos/text-analytics-how-to-call-api.md#json-schema) que contém os dados como texto não estruturado e não processado, em JSON.

3. Publique o pedido no ponto final estabelecido durante a inscrição, anexando o recurso pretendido: análise de sentimentos, extração de expressões-chave, deteção de idioma ou identificação de entidade.

4. Transmita a resposta em fluxo ou armazene-a localmente. Dependendo do pedido, os resultados são uma pontuação de sentimento, uma coleção de expressões-chave extraídas ou um código de idioma.

A saída é devolvida como um único documento JSON, com resultados para cada documento de texto que publicou, com base no ID. Posteriormente, pode analisar, visualizar ou categorizar os resultados em informações acionáveis.

Os dados não são armazenados na sua conta. As operações realizadas pela API de Análise de Texto não têm um estado específico, o que significa que o texto que fornecer é processado e os resultados são devolvidos imediatamente.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Linguagens suportadas

Esta secção foi movida para um artigo separado para melhor deteção. Consulte os [idiomas suportados na API de Análise de Texto](text-analytics-supported-languages.md) para este conteúdo.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de dados

Todos os pontos finais da API de Análise de Texto aceitam dados de texto não processados. O limite atual é 5,120 carateres para cada documento; Se precisar de analisar documentos maiores, poderá dividi-las em segmentos mais pequenos. Se necessitar de um limite maior, [contacte-nos](https://azure.microsoft.com/overview/sales-number/), para que possamos analisar os seus requisitos.

| Limite | Value |
|------------------------|---------------|
| Tamanho máximo de um documento único | 5,120 carateres como medido pelas [ `StringInfo.LengthInTextElements` ](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Tamanho máximo do pedido completo | 1 MB |
| Número máximo de documentos num pedido | 1000 documentos |

O limite de velocidade é de 100 chamadas por minuto. Tenha em atenção que pode submeter uma grande quantidade de documentos numa única chamada (até 1000 documentos).

## <a name="unicode-encoding"></a>Codificação Unicode

A API de Análise de Texto utiliza a codificação Unicode para representação de texto e cálculos de contagem de carateres. Os pedidos podem ser submetidos em UTF-8 e UTF-16, sem diferenças mensuráveis na contagem de carateres. Os pontos de código Unicode são utilizados como a heurística para o comprimento dos carateres, e são considerados equivalentes para efeitos de limites de dados de análise de texto. Se usar [ `StringInfo.LengthInTextElements` ](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) para obter a contagem de caracteres, estiver a utilizar o mesmo método que usamos para medir o tamanho dos dados.

## <a name="next-steps"></a>Passos Seguintes

Em primeiro lugar, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Pode colar uma entrada de texto (máximo de carateres 5,120) para detetar o idioma (até 120), calcular uma pontuação de sentimento ou extrair expressões-chave. Não é necessário inscrever-se.

Quando estiver pronto para chamar a API diretamente:

+ [Inscreva-se](how-tos/text-analytics-how-to-signup.md) para obter uma chave de acesso e reveja os passos para [chamar a API](how-tos/text-analytics-how-to-call-api.md).

+ O [Início Rápido](quickstarts/csharp.md) é uma descrição das chamadas à API REST escritas em C#. Saiba como submeter texto, escolher uma análise e ver os resultados com pouco código.

+ A [documentação de referência de API](//go.microsoft.com/fwlink/?LinkID=759346) oferece a documentação técnica para as APIs. A documentação suporta chamadas incorporadas, para que possa chamar a API a partir de cada página de documentação.

+ [Conteúdo Externo e da Comunidade](text-analytics-resource-external-community.md) fornece uma lista de mensagens de blogue e vídeos que demonstram como utilizar a Análise de Texto com outras ferramentas e tecnologias.

## <a name="see-also"></a>Consulte também

 [Página de Documentação dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/)
