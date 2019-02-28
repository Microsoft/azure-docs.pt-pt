---
title: Metadados com GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: A ferramenta QnA Maker permite-lhe adicionar metadados, na forma de pares chave/valor, a seus conjuntos de pergunta/resposta. Estas informações podem ser utilizadas para filtrar os resultados de consultas do utilizador e para armazenar informações adicionais que podem ser utilizadas no seguimento conversas.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/21/2019
ms.author: tulasim
ms.openlocfilehash: 462dfb2de8608eebd5609f7044bde03991fca3ca
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958053"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>Obter uma resposta de dados de conhecimento com a API de GenerateAnswer e metadados

Para obter a resposta prevista à pergunta de um utilizador, utilize a API de GenerateAnswer. Quando publica uma base de dados de conhecimento, esta informação para utilizar esta API é apresentada na página de publicação. Também pode configurar a API para filtrar com base em etiquetas de metadados de respostas e testar a base de dados de conhecimento a partir do ponto final com o parâmetro de cadeia de caracteres de consulta de teste.

A ferramenta QnA Maker permite-lhe adicionar metadados, na forma de pares de chave e valor, a seus conjuntos de pergunta/resposta. Estas informações podem ser utilizadas para filtrar os resultados de consultas do utilizador e para armazenar informações adicionais que podem ser utilizadas no seguimento conversas. Para obter mais informações, consulte [base de dados de conhecimento](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>Armazenamento de perguntas e respostas com uma entidade de QnA

Em primeiro lugar é importante compreender como o QnA Maker armazena os dados de pergunta/resposta. A ilustração seguinte mostra uma entidade de QnA:

![Entidade de QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade do QnA tem um ID exclusivo e persistente. O ID pode ser utilizado para disponibilizar as atualizações para uma entidade específica do QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obter previsões de resposta com a API de GenerateAnswer

Utilize a API de GenerateAnswer na sua aplicação ou Bot para consultar a base de dados de conhecimento com uma pergunta de utilizador para obter a melhor correspondência dos conjuntos de pergunta/resposta.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter o ponto final de GenerateAnswer 

Depois de publicar a sua base de dados de conhecimento, a partir do [portal do QnA Maker](https://www.qnamaker.ai), ou utilizando o [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), pode obter os detalhes do ponto final do GenerateAnswer.

Para obter os detalhes do seu ponto final:
1. Inicie sessão no [ https://www.qnamaker.ai ](https://www.qnamaker.ai).
1. Na **meu bases de dados de conhecimento**, clique em **View Code** para sua base de dados de conhecimento.
    ![meu bases de dados de conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenha os detalhes do seu ponto final de GenerateAnswer.

    ![Detalhes do ponto final](../media/qnamaker-how-to-metadata-usage/view-code.png)

Também pode obter os detalhes do seu ponto final da **definições** separador da sua base de dados de conhecimento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de pedido de GenerateAnswer

Chamar GenerateAnswer com um pedido HTTP POST. Para o código de exemplo que mostra como chamar GenerateAnswer, consulte a [inícios Rápidos](../quickstarts/csharp.md).

O **URL do pedido** tem o seguinte formato: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer?isTest=true
```

|Propriedade de pedido HTTP|Name|Type|Objetivo|
|--|--|--|--|
|Parâmetro de rota de URL|ID da base de dados de conhecimento|string|O GUID de sua base de dados de conhecimento.|
|Parâmetro de rota de URL|Anfitrião de ponto final do QnAMaker|string|O nome de anfitrião do ponto final implementado na sua subscrição do Azure. Está disponível na página definições depois de publicar a base de dados de conhecimento. |
|Cabeçalho|Content-Type|string|O tipo de suporte do corpo enviado para a API. Valor predefinido é: '|
|Cabeçalho|Autorização|string|A chave de ponto final (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo da mensagem|Objeto JSON|JSON|A pergunta com as definições|
|Parâmetro de cadeia de caracteres de consulta (opcional)|`isTest`|boolean|Se definido como true, devolve resultados de `testkb` índice de pesquisa, em vez de índice publicado.|

O corpo JSON tem várias definições:

|Propriedade de corpo JSON|Necessário|Type|Objetivo|
|--|--|--|--|
|`question`|obrigatório|string|Uma pergunta do utilizador sejam enviados para a sua base de dados de conhecimento.|
|`top`|opcional|inteiro|O número de resultados classificados a incluir na saída. O valor predefinido é 1.|
|`userId`|opcional|string|Um ID exclusivo para identificar o utilizador. Este ID será registado nos logs de bate-papo.|
|`strictFilters`|opcional|string|Se for especificado, informa ao QnA Maker para devolver apenas as respostas que tenham os metadados especificados.|

Um exemplo de corpo JSON é semelhante a:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriedades de resposta de GenerateAnswer

Uma resposta com êxito retorna um status de 200 e uma resposta JSON. 

|Propriedade de respostas (classificada por classificação)|Objetivo|
|--|--|
|pontuação|Uma pontuação de classificação, entre 0 e 100.|
|Id|Um ID exclusivo atribuído para a resposta.|
|Perguntas|As perguntas fornecidas pelo usuário.|
|Resposta|A resposta à pergunta.|
|source|O nome da origem do qual a resposta foi extraída ou guardada na base de dados de conhecimento.|
|do IdP|Os metadados associados com a resposta.|
|metadata.name|Nome de metadados. (string, comprimento máximo: 100, necessária)|
|metadata.value: Valor de metadados. (string, comprimento máximo: 100, necessária)|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>Utilizando os metadados permite-lhe filtrar respostas por etiquetas de metadados personalizados

Adicionar metadados permite-lhe filtrar as respostas por estas etiquetas de metadados. Considere o abaixo dados de FAQ. Adicione metadados a sua base de dados de conhecimento ao clicar no ícone de metadados.

![adicionar metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar os resultados com strictFilters para marcas de metadados

Considere a pergunta do utilizador "Quando o feche esta hotel?" em que a intenção é implícito para o restaurante "Paraíso."

Uma vez que os resultados são necessários apenas para o restaurante "Paraíso", pode definir um filtro na chamada GenerateAnswer nos metadados "Restaurante Name", da seguinte forma.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Utilize os resultados de perguntas e respostas para manter o contexto de conversação

A resposta para o GenerateAnswer contém as informações de metadados correspondente do conjunto correspondente de pergunta/resposta. Estas informações podem ser utilizadas na sua aplicação de cliente para armazenar o contexto da conversa anterior para utilização posterior conversas. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Página de publicação também fornece informações para gerar uma resposta com [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) e [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./create-knowledge-base.md)
