---
title: Realizar análise de sentimento com Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como detetar o sentimento em texto com o Texto De Análise de Texto dos Serviços Cognitivos Azure REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/27/2020
ms.author: aahi
ms.openlocfilehash: 99a62daf6dced88efd9bda591a0ca44a8b259a75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195643"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Como: Detetar o sentimento utilizando a API de Análise de Texto

A funcionalidade de Análise de Sentimentos da API avalia o texto e devolve pontuações de sentimento e etiquetas para cada frase. Isto é útil para detetar sentimentos positivos e negativos nas redes sociais, avaliações de clientes, fóruns de discussão e muito mais. Os modelos de IA utilizados pela API são fornecidos pelo serviço, basta enviar conteúdo para análise.

> [!TIP]
> O Text Analytics também fornece uma imagem de recipiente Docker baseada em Linux para deteção de idiomas, para que possa [instalar e executar o recipiente Text Analytics](text-analytics-how-to-install-containers.md) perto dos seus dados.

A Análise de Sentimentos suporta uma vasta gama de línguas, com mais em pré-visualização. Para obter mais informações, veja [Idiomas suportados](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Conceitos

O Text Analytics API usa um algoritmo de classificação de aprendizagem automática para gerar uma pontuação de sentimento entre 0 e 1. As pontuações próximas de 1 indicam um sentimento positivo, enquanto as próximas de 0 indicam um sentimento negativo. A análise de sentimento é realizada em todo o documento, em vez de entidades individuais no texto. Isto significa que as pontuações de sentimento são devolvidas a um nível de documento ou sentença. 

O modelo utilizado é pré-treinado com um extenso corpus de associações de texto e sentimento. Utiliza uma combinação de técnicas de análise, incluindo processamento de texto, análise parcial da fala, colocação de palavras e associações de palavras. Para obter mais informações sobre o algoritmo, veja [Introdução à Análise de Texto](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Atualmente, não é possível fornecer os seus próprios dados de treino. 

Há uma tendência para marcar precisão para melhorar quando os documentos contêm menos frases em vez de um grande bloco de texto. Durante a fase de avaliação de objetividade, o modelo determina se um documento como um todo é objetivo ou se contém sentimentos. Um documento que é maioritariamente objetivo não avança para a fase de deteção de sentimentos, o que resulta numa pontuação de 0,50, sem mais processamento. Para documentos que continuam no oleoduto, a próxima fase gera uma pontuação acima ou inferior a 0,50. A pontuação depende do grau de sentimento detetado no documento.

## <a name="sentiment-analysis-versions-and-features"></a>Versões e funcionalidades de Análise de Sentimento

O Text Analytics API oferece duas versões de Análise de Sentimentos - v2 e v3. A Análise de Sentimento sonante v3 (pré-visualização pública) proporciona melhorias significativas na precisão e detalhe da categorização e pontuação de texto da API.

> [!NOTE]
> * O formato de pedido de análise de sentimento v3 e [os limites de dados](../overview.md#data-limits) são os mesmos que a versão anterior.
> * A Análise de Sentimento si v3 `Central US`está `East Asia` `East US`disponível nas seguintes `West US 2`regiões: `Australia East` `Central Canada`. `South Central US` `UK South` `West Europe` `East US 2` `North Europe`, , , `Southeast Asia`, , , , , , e .

| Funcionalidade                                   | Análise de Sentimento v2 | Análise de Sentimento v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Métodos para pedidos individuais e de lote    | X                     | X                     |
| Pontuações de sentimento para todo o documento  | X                     | X                     |
| Pontuações de sentimento para frases individuais |                       | X                     |
| Rotulagem de sentimentos                        |                       | X                     |
| Versão do modelo                   |                       | X                     |

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

### <a name="sentiment-scoring"></a>Pontuação de sentimento

A Análise de Sentimento supor o texto com rótulos de sentimento (descrito abaixo). As pontuações devolvidas representam a confiança do modelo de que o texto é positivo, negativo ou neutro. Valores mais elevados significam maior confiança. 

### <a name="sentiment-labeling"></a>Rotulagem de sentimentos

A Análise de Sentimento st3 devolve rótulos `negative`de `neutral`sentimento a nível de frase e documentos (`positive`e ) juntamente com pontuações de confiança. O `mixed` rótulo de sentimento também pode ser devolvido ao nível do documento. 

O sentimento do documento é determinado abaixo:

| Sentimento de sentença                                                                            | Etiqueta de documento devolvido |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Pelo menos `positive` uma frase está no documento. O resto das frases são. `neutral` | `positive`              |
| Pelo menos `negative` uma frase está no documento. O resto das frases são. `neutral` | `negative`              |
| Pelo menos `negative` uma frase `positive` e pelo menos uma sentença estão no documento.    | `mixed`                 |
| Todas as frases `neutral`no documento são.                                                  | `neutral`               |

### <a name="model-versioning"></a>Versão do modelo

> [!NOTE]
> A versão do modelo para análise `v3.0-preview.1`de sentimentos está disponível a partir da versão .

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Código Exemplo C#

Pode encontrar uma aplicação de exemplo C# que chama esta versão da Análise de Sentimentos no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs).


#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

### <a name="sentiment-scoring"></a>Pontuação de sentimento

O analisador de sentimentos classifica o texto como predominantemente positivo ou negativo. Atribui uma pontuação na gama de 0 a 1. Os valores próximos de 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma corda não pode ser analisada por sentimento ou não tem sentimento, a pontuação é sempre 0,5 exatamente. Por exemplo, se passar por uma cadeia de carateres em espanhol com um código de idioma em inglês, a pontuação é 0,5.

---

## <a name="sending-a-rest-api-request"></a>Envio de um pedido de API de REPOUSO 

### <a name="preparation"></a>Preparação

A análise de sentimentos produz um resultado de maior qualidade quando lhe dás quantidades menores de texto para trabalhar. O mesmo já não acontece com a extração de expressões-chave, que tem um melhor desempenho com blocos de texto maiores. Para obter os melhores resultados com as duas operações, pondere reestruturar as entradas em conformidade.

Deve ter documentos JSON neste formato: ID, texto e linguagem.

O tamanho do documento deve ser inferior a 5.120 caracteres por documento. Pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido.

## <a name="structure-the-request"></a>Estruturar o pedido

Crie um pedido POST. Pode [utilizar](text-analytics-how-to-call-api.md) o Carteiro ou a consola de **teste API** nos seguintes links de referência para estruturar e enviar uma. 

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

[Referência de Análise de Sentimento v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[Referência de Análise de Sentimento v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Detete o ponto final HTTPS para análise de sentimentos utilizando um recurso De Texto Analytics no Azure ou num recipiente instantâneo de Análise de [Texto.](text-analytics-how-to-install-containers.md) Deve incluir o URL correto para a versão que pretende utilizar. Por exemplo:

> [!NOTE]
> Pode encontrar a sua chave e ponto final para o seu recurso Text Analytics no portal azul. Eles estarão localizados na página de **arranque rápida** do recurso, sob gestão de **recursos.** 

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Detete um cabeçalho de pedido para incluir a sua chave API de Análise de Texto. No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

### <a name="example-sentiment-analysis-request"></a>Pedido de análise de sentimento de exemplo 

Segue-se um exemplo de conteúdos que poderá enviar para a análise de sentimentos. O formato de pedido é o mesmo para ambas as versões da API.
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>Poste o pedido

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e número de pedidos que pode enviar por minuto e segundo, consulte a secção de [limites](../overview.md#data-limits) de dados na visão geral.

A API de Análise de Texto é apátrida. Não são armazenados dados na sua conta e os resultados são devolvidos imediatamente na resposta.


### <a name="view-the-results"></a>Ver os resultados

O analisador de sentimentos classifica o texto como predominantemente positivo ou negativo. Atribui uma pontuação na gama de 0 a 1. Os valores próximos de 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma corda não pode ser analisada por sentimento ou não tem sentimento, a pontuação é sempre 0,5 exatamente. Por exemplo, se passar por uma cadeia de carateres em espanhol com um código de idioma em inglês, a pontuação é 0,5.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite a JSON ou guardar a saída para um ficheiro no sistema local. Em seguida, importe a produção para uma aplicação que pode usar para classificar, pesquisar e manipular os dados. Devido ao suporte multilingual e emoji, a resposta pode conter compensações de texto. Veja [como processar compensações](../concepts/text-offsets.md) para mais informações.

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Resposta exemplo de análise de sentimento v3

As respostas da Sentiment Analysis v3 contêm etiquetas de sentimento e pontuações para cada frase e documento analisados. `documentScores`não é devolvido se o `mixed`rótulo de sentimento do documento for .

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Resposta de exemplo de análise de sentimento v2

As respostas da Sentiment Analysis v2 contêm pontuações de sentimento para cada documento enviado.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para análise de sentimentos usando a API text Analytics. Em resumo:

+ A Análise de Sentimentos está disponível para idiomas selecionados em duas versões.
+ Os documentos da JSON no organismo de pedido incluem um código de identificação, texto e idioma.
+ O pedido do `/sentiment` POST é para um ponto final usando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que é válido para a sua subscrição.
+ A saída de resposta, que consiste numa pontuação de sentimento para cada id de documento, pode ser transmitida para qualquer aplicação que aceite a JSON. Por exemplo, Excel e Power BI.

## <a name="see-also"></a>Consulte também

* [Descrição Geral da Análise de Texto](../overview.md)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
