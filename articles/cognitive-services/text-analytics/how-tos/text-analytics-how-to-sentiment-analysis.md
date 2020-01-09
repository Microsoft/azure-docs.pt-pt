---
title: Executar análise de sentimentos com Análise de Texto API REST
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como detectar sentimentos em texto com os serviços cognitivas do Azure Análise de Texto API REST.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 214c071e0d01908e2d46c932fcf87906de834102
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644686"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Como: detectar sentimentos usando o API de Análise de Texto

O recurso de Análise de Sentimento do API de Análise de Texto avalia o texto e retorna pontuações e rótulos de sentimentos para cada sentença. Isso é útil para detectar uma opinião positiva e negativa em mídia social, revisões de clientes, fóruns de discussão e muito mais. Os modelos de ia usados pela API são fornecidos pelo serviço, você só precisa enviar conteúdo para análise.

> [!TIP]
> O Análise de Texto também fornece uma imagem de contêiner do Docker baseada em Linux para detecção de idioma, para que você possa [instalar e executar o contêiner de análise de texto](text-analytics-how-to-install-containers.md) perto dos seus dados.

Análise de Sentimento dá suporte a uma ampla gama de linguagens, com mais em versão prévia. Para obter mais informações, veja [Idiomas suportados](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Conceitos

O API de Análise de Texto usa um algoritmo de classificação de aprendizado de máquina para gerar uma pontuação de sentimentos entre 0 e 1. As pontuações próximas de 1 indicam um sentimento positivo, enquanto as próximas de 0 indicam um sentimento negativo. A análise de sentimentos é executada em todo o documento, em vez de entidades individuais no texto. Isso significa que as pontuações de sentimentos são retornadas em um nível de documento ou frase. 

O modelo usado é pré-treinado com uma ampla corpus de associações de texto e de sentimentos. Ele utiliza uma combinação de técnicas para análise, incluindo processamento de texto, análise de parte da fala, posicionamento de palavras e associações de palavras. Para obter mais informações sobre o algoritmo, veja [Introdução à Análise de Texto](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Atualmente, não é possível fornecer seus próprios dados de treinamento. 

Há uma tendência para a precisão da pontuação para melhorar quando os documentos contêm menos frases em vez de um grande bloco de texto. Durante a fase de avaliação de objetividade, o modelo determina se um documento como um todo é objetivo ou se contém sentimentos. Um documento que é principalmente objetivo não progride para a fase de detecção de sentimentos, que resulta em uma pontuação de 0,50, sem nenhum processamento adicional. Para documentos que continuam no pipeline, a próxima fase gera uma pontuação acima ou abaixo de 0,50. A pontuação depende do grau de sentimentos detectado no documento.

## <a name="sentiment-analysis-versions-and-features"></a>Versões e recursos do Análise de Sentimento

O API de Análise de Texto oferece duas versões do Análise de Sentimento-V2 e v3. O Análise de Sentimento v3 (visualização pública) fornece melhorias significativas na precisão e nos detalhes da categorização e da Pontuação de texto da API.

> [!NOTE]
> * O formato de solicitação e [os limites de dados](../overview.md#data-limits) do análise de sentimento v3 são os mesmos da versão anterior.
> * O Análise de Sentimento v3 está disponível nas seguintes regiões: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`e `West US 2`.

| Funcionalidade                                   | Análise de Sentimento v2 | Análise de Sentimento v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Métodos para solicitações únicas e em lote    | X                     | X                     |
| Pontuações de sentimentos para o documento inteiro  | X                     | X                     |
| Pontuações de sentimentos para frases individuais |                       | X                     |
| Rotulação de sentimentos                        |                       | X                     |
| Controle de versão de modelo                   |                       | X                     |

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

### <a name="sentiment-scoring"></a>Pontuação de sentimentos

O analisador de sentimentos classifica o texto como predominantemente positivo ou negativo. Ele atribui uma pontuação no intervalo de 0 a 1. Os valores próximos de 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma cadeia de caracteres não pode ser analisada quanto a sentimentos ou não tem nenhuma opinião, a pontuação é sempre 0,5 exatamente. Por exemplo, se passar por uma cadeia de carateres em espanhol com um código de idioma em inglês, a pontuação é 0,5.


#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (visualização pública)](#tab/version-3)

### <a name="sentiment-scoring"></a>Pontuação de sentimentos

Análise de Sentimento v3 classifica o texto com rótulos de sentimentos (descritos abaixo). As pontuações retornadas representam a confiança do modelo de que o texto é positivo, negativo ou neutro. Valores mais altos significam maior confiança. 

### <a name="sentiment-labeling"></a>Rotulação de sentimentos

Análise de Sentimento v3 pode retornar pontuações e rótulos em um nível de frase e documento. As pontuações e os rótulos são `positive`, `negative`e `neutral`. No nível do documento, o `mixed` rótulo de sentimentos também pode ser retornado sem uma pontuação. A sunotação do documento é determinada abaixo:

| Sentimentos de frase                                                                            | Rótulo do documento retornado |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Pelo menos uma `positive` sentença está no documento. O restante das frases é `neutral`. | `positive`              |
| Pelo menos uma `negative` sentença está no documento. O restante das frases é `neutral`. | `negative`              |
| Pelo menos uma frase `negative` e pelo menos uma frase `positive` está no documento.    | `mixed`                 |
| Todas as frases no documento são `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Controle de versão de modelo

> [!NOTE]
> O controle de versão de modelo para análise de sentimentos está disponível a partir da versão `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Código C# de exemplo

Você pode encontrar um aplicativo C# de exemplo que chama essa versão do análise de sentimento no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

---

## <a name="sending-a-rest-api-request"></a>Enviando uma solicitação da API REST 

### <a name="preparation"></a>Preparação

A análise de sentimentos produz um resultado de qualidade mais alta quando você dá a ele quantidades menores de texto para trabalhar. O mesmo já não acontece com a extração de expressões-chave, que tem um melhor desempenho com blocos de texto maiores. Para obter os melhores resultados com as duas operações, pondere reestruturar as entradas em conformidade.

Você deve ter documentos JSON neste formato: ID, texto e idioma.

O tamanho do documento deve ter menos de 5.120 caracteres por documento. Você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido.

## <a name="structure-the-request"></a>Estruturar a solicitação

Crie uma solicitação POST. Você pode [usar o postmaster](text-analytics-how-to-call-api.md) ou o **console de teste de API** nos links de referência a seguir para estruturar e enviar rapidamente um. 

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

[Referência de Análise de Sentimento v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (visualização pública)](#tab/version-3)

[Referência do Análise de Sentimento v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

---

Defina o ponto de extremidade HTTPS para análise de sentimentos usando um recurso de Análise de Texto no Azure ou um [contêiner de análise de texto](text-analytics-how-to-install-containers.md)instanciado. Você deve incluir a URL correta para a versão que deseja usar. Por exemplo:
    
[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (visualização pública)](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

---

Defina um cabeçalho de solicitação para incluir sua chave de API de Análise de Texto. No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

### <a name="example-sentiment-analysis-request"></a>Exemplo de solicitação de Análise de Sentimento 

Segue-se um exemplo de conteúdos que poderá enviar para a análise de sentimentos. O formato da solicitação é o mesmo para as duas versões da API.
    
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

### <a name="post-the-request"></a>Postar a solicitação

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo, consulte a seção [limites de dados](../overview.md#data-limits) na visão geral.

O API de Análise de Texto é sem monitoração de estado. Nenhum dado é armazenado em sua conta e os resultados são retornados imediatamente na resposta.


### <a name="view-the-results"></a>Ver os resultados

O analisador de sentimentos classifica o texto como predominantemente positivo ou negativo. Ele atribui uma pontuação no intervalo de 0 a 1. Os valores próximos de 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma cadeia de caracteres não pode ser analisada quanto a sentimentos ou não tem nenhuma opinião, a pontuação é sempre 0,5 exatamente. Por exemplo, se passar por uma cadeia de carateres em espanhol com um código de idioma em inglês, a pontuação é 0,5.

O resultado é devolvido imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salva a saída em um arquivo no sistema local. Em seguida, importe a saída para um aplicativo que você pode usar para classificar, Pesquisar e manipular os dados.

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Resposta de exemplo do Análise de Sentimento v2

As respostas da Análise de Sentimento v2 contêm pontuações de sentimentos para cada documento enviado.

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

#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (visualização pública)](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Resposta de exemplo do Análise de Sentimento v3

As respostas de Análise de Sentimento v3 contêm rótulos de opiniões e pontuações para cada documento e frase analisada. `documentScores` não será retornado se o rótulo de sentimentos do documento for `mixed`.

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
---

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para análise de sentimentos usando o API de Análise de Texto. Em resumo:

+ Análise de Sentimento está disponível para os idiomas selecionados em duas versões.
+ Os documentos JSON no corpo da solicitação incluem uma ID, um texto e um código de idioma.
+ A solicitação POST é para um ponto de extremidade `/sentiment` usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para sua assinatura.
+ A saída de resposta, que consiste em uma pontuação de sentimentos para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceite JSON. Por exemplo, Excel e Power BI.

## <a name="see-also"></a>Ver também

* [Descrição Geral da Análise de Texto](../overview.md)
* [Usando a biblioteca de cliente Análise de Texto](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
