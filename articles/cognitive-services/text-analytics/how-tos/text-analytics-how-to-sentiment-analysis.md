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
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: a930a22472dd4329c555539704747af9a2359c33
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837153"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Exemplo: detectar sentimentos com Análise de Texto

A [API de análise de sentimento do Azure](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) avalia a entrada de texto e retorna uma pontuação de sentimentos para cada documento. Pontuações variam de 0 (negativo) a 1 (positivo).

Esta funcionalidade é útil para detetar sentimentos positivos e negativos nas redes sociais, em críticas de clientes e fóruns de discussão. O conteúdo é fornecido por você. Os modelos e os dados de treinamento são fornecidos pelo serviço.

Atualmente, a API de Análise de Sentimento dá suporte a inglês, alemão, espanhol e francês. Os outros idiomas estão em pré-visualização. Para obter mais informações, veja [Idiomas suportados](../text-analytics-supported-languages.md).

> [!TIP]
> O API de Análise de Texto do Azure também fornece uma imagem de contêiner do Docker baseada em Linux para análise de sentimentos, para que você possa [instalar e executar o contêiner de análise de texto](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="concepts"></a>Conceitos

A Análise de Texto utiliza um algoritmo de classificação de aprendizagem automática para gerar uma pontuação de sentimento entre 0 e 1. As pontuações próximas de 1 indicam um sentimento positivo, enquanto as próximas de 0 indicam um sentimento negativo. O modelo é previamente preparado com um corpo de texto extenso com associações de sentimentos. Atualmente, não é possível fornecer seus próprios dados de treinamento. O modelo usa uma combinação de técnicas durante a análise de texto. As técnicas incluem processamento de texto, análise de parte da fala, posicionamento de palavras e associações de palavras. Para obter mais informações sobre o algoritmo, veja [Introdução à Análise de Texto](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

A análise de sentimentos é realizada em todo o documento em vez de extrair sentimentos de uma entidade particular no texto. Na prática, há uma tendência para a precisão da pontuação para melhorar quando os documentos contêm uma ou duas frases em vez de um grande bloco de texto. Durante a fase de avaliação de objetividade, o modelo determina se um documento como um todo é objetivo ou se contém sentimentos. Um documento que é principalmente objetivo não progride para a fase de detecção de sentimentos, que resulta em uma pontuação de 0,50, sem nenhum processamento adicional. Para documentos que continuam no pipeline, a próxima fase gera uma pontuação acima ou abaixo de 0,50. A pontuação depende do grau de sentimentos detectado no documento.

## <a name="sentiment-analysis-v3-public-preview"></a>Visualização pública do Análise de Sentimento v3

A [próxima versão do análise de sentimento](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/56f30ceeeda5650db055a3c9) agora está disponível para visualização pública. Ele fornece melhorias significativas na precisão e nos detalhes da categorização e da Pontuação de texto da API.

> [!NOTE]
> * O formato de solicitação e [os limites de dados](../overview.md#data-limits) do análise de sentimento v3 são os mesmos da versão anterior.
> * Neste momento, Análise de Sentimento V3:
>    * Atualmente, dá suporte ao inglês (`en`), japonês (`ja`), chinês simplificado (`zh-Hans`), chinês tradicional (`zh-Hant`), francês (`fr`), italiano (`it`), espanhol (`es`), holandês (`nl`), Português (`pt`) e alemão (`de`) idiomas.
>    * Está disponível nas seguintes regiões: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`e `West US 2`.

|Funcionalidade |Descrição  |
|---------|---------|
|Precisão aprimorada     | melhoria significativa na deteção de sentimentos positivos, neutros, negativos e mistos em documentos de texto em relação às versões anteriores.           |
|Pontuação de sentimentos de nível de documento e de sentença     | deteção do sentimento de um documento e das respetivas frases individuais. Se o documento incluir várias frases, a cada frase é também atribuída uma classificação de sentimento.         |
|Rotulagem e pontuação de sentimentos     | A API agora retorna categorias de sentimentos para texto, além de uma pontuação de sentimentos. As categorias são `positive`, `negative`, `neutral`e `mixed`.       |
| Saída aprimorada | A análise de sentimentos agora retorna informações para um documento de texto inteiro e suas frases individuais. |
| parâmetro de versão de modelo | Um parâmetro opcional para escolher qual versão do modelo de Análise de Texto é usada em seus dados. |

### <a name="sentiment-labeling"></a>Rotulação de sentimentos

Análise de Sentimento v3 pode retornar pontuações e rótulos em um nível de frase e documento. As pontuações e os rótulos são `positive`, `negative`e `neutral`. No nível do documento, o `mixed` rótulo de sentimentos (não a pontuação) também pode ser retornado. A innotação do documento é determinada pela agregação das pontuações das frases.

| Sentimentos de frase                                                        | Rótulo do documento retornado |
|---------------------------------------------------------------------------|----------------|
| Pelo menos uma frase positiva e o restante das frases são neutros. | `positive`     |
| Pelo menos uma frase negativa e o restante das frases são neutros.  | `negative`     |
| Pelo menos uma frase negativa e pelo menos uma frase positiva.         | `mixed`        |
| Todas as frases são neutras.                                                 | `neutral`      |

### <a name="model-versioning"></a>Controle de versão de modelo

> [!NOTE]
> O controle de versão de modelo para análise de sentimentos está disponível a partir da versão `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="sentiment-analysis-v3-example-request"></a>Solicitação de exemplo do Análise de Sentimento v3

O JSON a seguir é um exemplo de uma solicitação feita à nova versão do Análise de Sentimento. A formatação da solicitação é igual à versão anterior:

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

### <a name="sentiment-analysis-v3-example-response"></a>Resposta de exemplo do Análise de Sentimento v3

Embora o formato da solicitação seja igual à versão anterior, o formato da resposta foi alterado. O JSON a seguir é um exemplo de resposta da nova versão da API:

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

### <a name="example-c-code"></a>Código C# de exemplo

Você pode encontrar um aplicativo C# de exemplo que chama essa versão do análise de sentimento no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="preparation"></a>Preparação

A análise de sentimentos produz um resultado de qualidade mais alta quando você dá a ele partes menores de texto para trabalhar. O mesmo já não acontece com a extração de expressões-chave, que tem um melhor desempenho com blocos de texto maiores. Para obter os melhores resultados com as duas operações, pondere reestruturar as entradas em conformidade.

Você deve ter documentos JSON neste formato: ID, texto e idioma.

O tamanho do documento deve ter menos de 5.120 caracteres por documento. Você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O exemplo a seguir é um exemplo de conteúdo que você pode enviar para análise de sentimentos:

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Passo 1: estruturar o pedido

Para obter mais informações sobre a definição de solicitação, consulte [chamar o API de análise de texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie uma solicitação POST. Para examinar a documentação da API para essa solicitação, consulte a [api análise de sentimento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Defina o ponto de extremidade HTTP para análise de sentimentos usando um recurso de Análise de Texto no Azure ou um [contêiner de análise de texto](text-analytics-how-to-install-containers.md)instanciado. Você deve incluir `/text/analytics/v2.1/sentiment` na URL. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`.

+ Defina um cabeçalho de solicitação para incluir a [chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de análise de texto.

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Use o [postmaster](text-analytics-how-to-call-api.md) ou abra o **console de teste de API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) para estruturar a solicitação e lançá-la para o serviço.

## <a name="step-2-post-the-request"></a>Passo 2: publicar o pedido

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo, consulte a seção [limites de dados](../overview.md#data-limits) na visão geral.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.


## <a name="step-3-view-the-results"></a>Etapa 3: exibir os resultados

O analisador de sentimentos classifica o texto como predominantemente positivo ou negativo. Ele atribui uma pontuação no intervalo de 0 a 1. Os valores próximos de 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma cadeia de caracteres não pode ser analisada quanto a sentimentos ou não tem nenhuma opinião, a pontuação é sempre 0,5 exatamente. Por exemplo, se passar por uma cadeia de carateres em espanhol com um código de idioma em inglês, a pontuação é 0,5.

O resultado é devolvido imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salva a saída em um arquivo no sistema local. Em seguida, importe a saída para um aplicativo que você pode usar para classificar, Pesquisar e manipular os dados.

O exemplo a seguir mostra a resposta para a coleção de documentos neste artigo:

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para análise de sentimentos usando Análise de Texto nos serviços cognitivas do Azure. Em resumo:

+ A [API de análise de sentimento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) está disponível para os idiomas selecionados.
+ Os documentos JSON no corpo da solicitação incluem uma ID, um texto e um código de idioma.
+ A solicitação POST é para um ponto de extremidade `/sentiment` usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para sua assinatura.
+ A saída de resposta, que consiste em uma pontuação de sentimentos para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceite JSON. Aplicativos de exemplo incluem Excel e Power BI, para citar alguns.

## <a name="see-also"></a>Consultar também

 [Perguntas](../text-analytics-resource-faq.md) frequentes [sobre análise de texto visão geral](../overview.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extrair expressões-chave](text-analytics-how-to-keyword-extraction.md)
