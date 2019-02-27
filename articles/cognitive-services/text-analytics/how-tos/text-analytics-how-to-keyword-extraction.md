---
title: Extração de expressões-chave com a API de REST de análise de texto | Documentos da Microsoft
description: Como extrair expressões-chave com a API de REST de análise de texto dos serviços cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: a427910c598ce7a93dd1b74fb4297d9825ba14ca
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887391"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exemplo: Como extrair expressões-chave com análise de texto

A [API de Extração de Expressões-Chave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) avalia o texto não estruturado e, para cada documento JSON, devolve uma lista de expressões-chave. 

Esta funcionalidade é útil se precisar de identificar rapidamente os pontos principais numa coleção de documentos. Por exemplo, para o texto de entrada "The food was delicious and there were wonderful staff", o serviço devolve os pontos de conversa principais: "food" e "wonderful staff".

Atualmente, a Extração de Expressões-Chave suporta inglês, alemão, espanhol e japonês. Os outros idiomas estão em pré-visualização. Para obter mais informações, veja [Idiomas suportados](../text-analytics-supported-languages.md).

> [!TIP]
> Análise de texto também fornece um Docker baseado em Linux imagem de contentor para extração de expressões-chave, para que possa [instalar e executar o contentor de análise de texto](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="preparation"></a>Preparação

Extração de expressões-chave funciona melhor quando dá maiores quantidades de texto para trabalhar em. Este é o oposto de análise de sentimentos, que tem um desempenho melhor em mais pequenas quantidades de texto. Para obter os melhores resultados com as duas operações, pondere reestruturar as entradas em conformidade.

Tem de ter documentos JSON neste formato: id, texto, idioma

Tamanho do documento tem de ser em 5,120 carateres por documento, e pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O seguinte exemplo é uma ilustração de conteúdos que pode enviar para extração de expressões-chave.

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
    
## <a name="step-1-structure-the-request"></a>Passo 1: O pedido de estrutura

Pode obter detalhes sobre a definição do pedido em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Reveja a documentação da API para este pedido: [API de expressões-chave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Definir o ponto final HTTP para extração de expressões-chave, usando um recurso de análise de texto no Azure ou um instanciadas [contentor de análise de texto](text-analytics-how-to-install-containers.md). Tem de incluir o recurso `/keyPhrases`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ Defina um cabeçalho de pedido para incluir a chave de acesso para operações de Análise de Texto. Para obter mais informações, veja [Como localizar pontos finais e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: O pedido de POST

A análise é realizada aquando da receção do pedido. O serviço aceita até 100 pedidos por minuto. Cada pedido pode ter um máximo de 1 MB.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.

## <a name="step-3-view-results"></a>Passo 3: Ver resultados

Todos os pedidos POST devolvem uma resposta formatada JSON com os IDs e as propriedades detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

Um exemplo de saída para extração de expressões-chave é mostrado aqui:

```json
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

Como observado, o analisador localiza e descarta palavras não essenciais e mantém termos ou frases individuais que parecem ser o assunto ou o objeto de uma frase. 

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxos de trabalho relativos à extração de expressões-chave com recurso à Análise de Texto nos Serviços Cognitivos. Em resumo:

+ A [API de extração de expressões-chave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) está disponível para alguns idiomas.
+ Os documentos JSON no corpo do pedido incluem um id, texto e código de idioma.
+ O pedido POST refere-se a um ponto final `/keyphrases` com recurso a uma [chave de acesso personalizada e um ponto final](text-analytics-how-to-access-key.md) válido para a sua subscrição.
+ O resultado da resposta, que consiste em palavras e expressões-chave para cada ID de documento, pode ser transmitido para qualquer aplicação que aceite JSON, incluindo, por exemplo, o Excel e o Power BI.

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [API de Análise de Texto](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
