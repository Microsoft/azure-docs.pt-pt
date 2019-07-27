---
title: Extração de frases-chave usando a API REST do Análise de Texto
titleSuffix: Azure Cognitive Services
description: Como extrair frases-chave usando a API REST do Análise de Texto dos serviços cognitivas do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 06/05/2019
ms.author: raymondl
ms.openlocfilehash: 58bfb889662a58aa02286c41a2e242e6a0e9a75c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562645"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exemplo: como extrair expressões-chave com a Análise de Texto

A [API de Extração de Expressões-Chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) avalia o texto não estruturado e, para cada documento JSON, devolve uma lista de expressões-chave. 

Esta funcionalidade é útil se precisar de identificar rapidamente os pontos principais numa coleção de documentos. Por exemplo, para o texto de entrada "The food was delicious and there were wonderful staff", o serviço devolve os pontos de conversa principais: "food" e "wonderful staff".

Consulte o artigo [idiomas com suporte](../text-analytics-supported-languages.md) para obter mais informações. 

> [!TIP]
> O Análise de Texto também fornece uma imagem de contêiner do Docker baseada em Linux para extração de frases-chave, para que você possa [instalar e executar o contêiner de análise de texto](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="preparation"></a>Preparação

A extração de frases-chave funciona melhor quando você dá a ela maiores quantidades de texto para trabalhar. Isso é oposto da análise de sentimentos, que é melhorada em quantidades menores de texto. Para obter os melhores resultados com as duas operações, pondere reestruturar as entradas em conformidade.

Tem de ter documentos JSON neste formato: id, texto, idioma

O tamanho do documento deve ter menos de 5.120 caracteres por documento e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O seguinte exemplo é uma ilustração de conteúdos que pode enviar para extração de expressões-chave.

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
    
## <a name="step-1-structure-the-request"></a>Passo 1: Estruturar a solicitação

Pode obter detalhes sobre a definição do pedido em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Examine a documentação da API para esta solicitação: [API de frases-chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)

+ Defina o ponto de extremidade HTTP para extração de frase-chave, usando um recurso de Análise de Texto no Azure ou um [contêiner de análise de texto](text-analytics-how-to-install-containers.md)instanciado. Tem de incluir o recurso `/keyPhrases`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`

+ Defina um cabeçalho de pedido para incluir a chave de acesso para operações de Análise de Texto. Para obter mais informações, veja [Como localizar pontos finais e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: Postar a solicitação

A análise é realizada aquando da receção do pedido. Consulte a seção [limites de dados](../overview.md#data-limits) na visão geral para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.

## <a name="step-3-view-results"></a>Passo 3: Ver resultados

Todos os pedidos POST devolvem uma resposta formatada JSON com os IDs e as propriedades detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

Um exemplo da saída para extração de frases-chave é mostrado aqui:

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

+ A [API de extração de expressões-chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) está disponível para alguns idiomas.
+ Os documentos JSON no corpo do pedido incluem um id, texto e código de idioma.
+ O pedido POST refere-se a um ponto final `/keyphrases` com recurso a uma [chave de acesso personalizada e um ponto final](text-analytics-how-to-access-key.md) válido para a sua subscrição.
+ O resultado da resposta, que consiste em palavras e expressões-chave para cada ID de documento, pode ser transmitido para qualquer aplicação que aceite JSON, incluindo, por exemplo, o Excel e o Power BI.

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [API de Análise de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
