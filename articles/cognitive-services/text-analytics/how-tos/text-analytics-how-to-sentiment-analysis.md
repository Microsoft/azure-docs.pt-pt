---
title: Análise de sentimentos com a análise de texto dos serviços cognitivos do Azure | Documentos da Microsoft
description: Saiba como detetar sentimentos com a API REST de Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 7287a9ddbd84960dcde790d813a6204e9e790094
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887425"
---
# <a name="example-how-to-detect-sentiment-with-text-analytics"></a>Exemplo: Como detetar sentimento com análise de texto

A [API de Análise de Sentimentos](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) avalia a entrada de texto e devolve uma pontuação de sentimento para cada documento entre 0 (negativo) e 1 (positivo).

Esta funcionalidade é útil para detetar sentimentos positivos e negativos nas redes sociais, em críticas de clientes e fóruns de discussão. Os conteúdos são fornecidos por si; os modelos e dados de preparação são fornecidos pelo serviço.

Atualmente, a Análise de Sentimentos suporta os seguintes idiomas: inglês, alemão, espanhol e francês. Os outros idiomas estão em pré-visualização. Para obter mais informações, veja [Idiomas suportados](../text-analytics-supported-languages.md).

> [!TIP]
> Análise de texto também fornece um Docker baseado em Linux imagem de contentor para a análise de sentimentos, para que possa [instalar e executar o contentor de análise de texto](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="concepts"></a>Conceitos

A Análise de Texto utiliza um algoritmo de classificação de aprendizagem automática para gerar uma pontuação de sentimento entre 0 e 1. As pontuações próximas de 1 indicam um sentimento positivo, enquanto as próximas de 0 indicam um sentimento negativo. O modelo é previamente preparado com um corpo de texto extenso com associações de sentimentos. Atualmente, não é possível fornecer os seus próprios dados de preparação. O modelo utiliza uma combinação de técnicas durante a análise de texto, incluindo o processamento de texto, a análise de parte do discurso, posicionamento de palavras e associações de palavras. Para obter mais informações sobre o algoritmo, veja [Introdução à Análise de Texto](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

A análise de sentimentos é realizada em todo o documento em vez de extrair sentimentos de uma entidade particular no texto. Na prática, a precisão da pontuação tende a melhorar quando os documentos contêm uma ou duas frases em vez de um grande bloco de texto. Durante a fase de avaliação de objetividade, o modelo determina se um documento como um todo é objetivo ou se contém sentimentos. Um documento que é maioritariamente objetivo não passa para a fase de deteção de sentimentos, o que resulta numa pontuação de 0,50 sem processamento adicional. No caso dos outros documentos, a fase seguinte gera uma pontuação acima ou abaixo de 0,50, consoante o grau de sentimento detetado no documento.

## <a name="preparation"></a>Preparação

A análise de sentimentos produz um resultado de qualidade superior quando são fornecidos segmentos de texto mais pequenos. O mesmo já não acontece com a extração de expressões-chave, que tem um melhor desempenho com blocos de texto maiores. Para obter os melhores resultados com as duas operações, pondere reestruturar as entradas em conformidade.

Tem de ter documentos JSON neste formato: id, texto, idioma

Tamanho do documento tem de ser em 5,120 carateres por documento, e pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. Segue-se um exemplo de conteúdos que poderá enviar para a análise de sentimentos.

```
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

## <a name="step-1-structure-the-request"></a>Passo 1: O pedido de estrutura

Pode obter detalhes sobre a definição do pedido em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Reveja a documentação da API para este pedido: [API de análise de sentimentos](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ Definir o ponto final HTTP para análise de sentimentos, usando um recurso de análise de texto no Azure ou um instanciadas [contentor de análise de texto](text-analytics-how-to-install-containers.md). Tem de incluir o recurso `/sentiment`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`

+ Defina um cabeçalho de pedido para incluir a chave de acesso para operações de Análise de Texto. Para obter mais informações, veja [Como localizar pontos finais e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) para estruturar o pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: O pedido de POST

A análise é realizada aquando da receção do pedido. O serviço aceita até 100 pedidos por minuto. Cada pedido pode ter um máximo de 1 MB.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.


## <a name="step-3-view-results"></a>Passo 3: Ver resultados

O analisador de sentimentos classifica o texto como predominantemente positivo ou negativo. Para tal, atribui uma pontuação entre 0 e 1. Os valores próximos de 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma cadeia de carateres não puder ser analisada em termos de sentimento ou não tiver sentimento, a pontuação será sempre exatamente 0,5. Por exemplo, se passar por uma cadeia de carateres em espanhol com um código de idioma em inglês, a pontuação é 0,5.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

O seguinte exemplo mostra a resposta para a coleção de documentos neste artigo.

```
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

Neste artigo, aprendeu conceitos e fluxos de trabalho relativos à análise de sentimentos com recurso à Análise de Texto nos Serviços Cognitivos. Em resumo:

+ A [API de Análise de Sentimentos](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) está disponível para alguns idiomas.
+ Os documentos JSON no corpo do pedido incluem um id, texto e código de idioma.
+ O pedido POST refere-se a um ponto final `/sentiment` com recurso a uma [chave de acesso personalizada e um ponto final](text-analytics-how-to-access-key.md) válido para a sua subscrição.
+ O resultado da resposta, que consiste numa pontuação de sentimento para cada ID de documento, pode ser transmitido para qualquer aplicação que aceite JSON, incluindo, por exemplo, o Excel e o Power BI.

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Extrair expressões-chave](text-analytics-how-to-keyword-extraction.md)
