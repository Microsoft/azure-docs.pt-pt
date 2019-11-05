---
title: 'Tutorial: prever intenções-LUIS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, crie um aplicativo personalizado que prevê a intenção de um usuário. Esta aplicação é o tipo mais simples de aplicação LUIS, porque não extrai vários elementos de dados do texto da expressão, como endereços de e-mail ou datas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 83ecf0767f2b21065c698421e3ad8f07f31d5b16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465280"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Tutorial: compilar o aplicativo LUIS para determinar as intenções do usuário

Neste tutorial, irá criar uma aplicação personalizada de Recursos Humanos (RH) que prevê a intenção de um utilizador com base na expressão (texto). 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * Criar uma nova aplicação 
> * Criar intenções
> * Adicionar expressões de exemplo
> * Preparar a aplicação
> * Publicar aplicação
> * Obter a intenção do ponto final


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Intenções do usuário como tentativas

A finalidade do aplicativo é determinar a intenção do texto de idioma natural, de conversação: 

`Are there any new positions in the Seattle office?`

Estas intenções estão categorizadas em **Intenções**. 

Esta aplicação tem algumas intenções. 

|Intenção|Objetivo|
|--|--|
|`ApplyForJob`|Determine se o usuário está se aplicando a um trabalho.|
|`GetJobInformation`|Determine se o usuário está procurando informações sobre trabalhos em geral ou um trabalho específico.|
|`None`|Determine se o usuário está solicitando algo que o aplicativo não deve responder. Essa tentativa se fornecida como parte da criação do aplicativo e não pode ser excluída. |

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Criar intenção para informações do trabalho

1. Selecione **Criar nova intenção**. Introduza o nome da nova intenção `GetJobInformation`. Essa intenção é prevista quando um usuário deseja informações sobre trabalhos abertos na empresa. 

    ![Captura de tela da caixa de diálogo Nova tentativa de Reconhecimento vocal (LUIS)](media/luis-quickstart-intents-only/create-intent.png "Captura de tela da caixa de diálogo Nova tentativa de Reconhecimento vocal (LUIS)")

1. Selecione **Done** (Concluído).

2. Adicione vários exemplos de declarações a essa intenção que você espera que um usuário pergunte:

    | Expressões de exemplo|
    |--|
    |`Any new jobs posted today?`|
    |`Are there any new positions in the Seattle office?`|
    |`Are there any remote worker or telecommute jobs open for engineers?`|
    |`Is there any work with databases?`|
    |`I'm looking for a co-working situation in the tampa office.`|
    |`Is there an internship in the san francisco office?`|
    |`Is there any part-time work for people in college?`|
    |`Looking for a new situation with responsibilities in accounting`|
    |`Looking for a job in new york city for bilingual speakers.`|
    |`Looking for a new situation with responsibilities in accounting.`|
    |`New jobs?`|
    |`Show me all the jobs for engineers that were added in the last 2 days.`|
    |`Today's job postings?`|
    |`What accounting positions are open in the london office?`|
    |`What positions are available for Senior Engineers?`|
    |`Where is the job listings`|

    [![Captura de tela de inserção de novo declarações para a tentativa de MyStore](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Captura de tela de inserção de novo declarações para a tentativa de MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Ao fornecer o _exemplo declarações_, você está treinando Luis sobre quais tipos de declarações devem ser previstos para essa intenção. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar declarações de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Treinar o aplicativo antes de testar ou publicar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar o aplicativo para consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Obter previsão de intenção do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá para o final do URL na barra de endereço e introduza `I'm looking for a job with Natural Language Processing`. O último parâmetro de cadeia de consulta é `q`, a expressão **query**. Esta expressão não é igual a nenhuma das expressões de exemplo. É um bom teste e deverá devolver a intenção `GetJobInformation` como a intenção com a melhor classificação. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    O parâmetro `verbose=true` QueryString significa incluir **todas as tentativas** nos resultados da consulta do aplicativo. A matriz de entidades está vazia porque esta aplicação não tem entidades atualmente. 

    O resultado JSON identifica a intenção com a melhor classificação como a propriedade **`topScoringIntent`** . Todas as classificações estão compreendidas entre 1 e 0, estando a melhor classificação próxima de 1. 

## <a name="create-intent-for-job-applications"></a>Criar intenção para aplicativos de trabalho

Retorne ao portal do LUIS e crie uma nova intenção para determinar se o usuário expressão está prestes a ser aplicado a um trabalho.

1. Selecione **Build** (Criar) no menu direito superior para regressar à criação da aplicação.

1. Selecione **tentativas** no menu à esquerda para obter a lista de tentativas.

1. Selecione **Create new intent** (Criar nova intenção) e introduza o nome `ApplyForJob`. 

    ![Caixa de diálogo do LUIS para criar a nova intenção](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Adicione várias expressões a esta intenção, que espera que um utilizador peça, tais como:

    | Expressões de exemplo|
    |--|
    |`Fill out application for Job 123456`|
    |`Here is my c.v. for position 654234`|
    |`Here is my resume for the part-time receptionist post.`|
    |`I'm applying for the art desk job with this paperwork.`|
    |`I'm applying for the summer college internship in Research and Development in San Diego`|
    |`I'm requesting to submit my resume to the temporary position in the cafeteria.`|
    |`I'm submitting my resume for the new Autocar team in Columbus, OH`|
    |`I want to apply for the new accounting job`|
    |`Job 456789 accounting internship paperwork is here`|
    |`Job 567890 and my paperwork`|
    |`My papers for the tulsa accounting internship are attached.`|
    |`My paperwork for the holiday delivery position`|
    |`Please send my resume for the new accounting job in seattle`|
    |`Submit resume for engineering position`|
    |`This is my c.v. for post 234123 in Tampa.`|


## <a name="train-again"></a>Preparar novamente

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Publicar novamente

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Obter previsão de intenção novamente

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na nova janela do browser, introduza `Can I submit my resume for job 235986` no final do URL. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    Os resultados incluem a nova intenção **ApplyForJob**, bem como as intenções existentes. 

## <a name="client-application-next-steps"></a>Próximas etapas do aplicativo cliente

Depois de devolver a resposta JSON, o LUIS conclui este pedido. O LUIS não fornece respostas às expressões do utilizador, apenas identifica o tipo de informação que está a ser pedida em linguagem natural. O acompanhamento de conversação é fornecido pelo aplicativo cliente, como um bot do Azure. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tipos de entidades](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Passos seguintes

Este tutorial criou a aplicação Recursos Humanos (RH), criou duas intenções, adicionou expressões de exemplo a cada intenção, adicionou expressões de exemplo à intenção None, preparou, publicou e testou no ponto final. Estes são os passos básicos de criação de um modelo do LUIS. 

Continue com este aplicativo, [adicionando uma entidade simples e uma lista de frases](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Adicionar intenções e entidades pré-concebidas a esta aplicação](luis-tutorial-prebuilt-intents-entities.md)
