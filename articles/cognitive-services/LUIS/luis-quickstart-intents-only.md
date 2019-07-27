---
title: Prever intenções-LUIS
titleSuffix: Azure Cognitive Services
description: Neste tutorial, crie um aplicativo personalizado que prevê a intenção de um usuário. Esta aplicação é o tipo mais simples de aplicação LUIS, porque não extrai vários elementos de dados do texto da expressão, como endereços de e-mail ou datas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 249ac689f855d175fa5df7cc5fed9438c0873289
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560390"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Tutorial: Compilar o aplicativo LUIS para determinar as intenções do usuário

Neste tutorial, irá criar uma aplicação personalizada de Recursos Humanos (RH) que prevê a intenção de um utilizador com base na expressão (texto). 

**Neste tutorial, vai aprender a:**

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
|ApplyForJob|Determine se o usuário está se aplicando a um trabalho.|
|GetJobInformation|Determine se o usuário está procurando informações sobre trabalhos em geral ou um trabalho específico.|
|Nenhuma|Determine se o usuário está solicitando algo que o aplicativo não deve responder. Essa tentativa se fornecida como parte da criação do aplicativo e não pode ser excluída. |

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Criar intenção para informações do trabalho

1. Selecione **Create new intent** (Criar nova intenção). Introduza o nome da nova intenção `GetJobInformation`. Essa intenção é prevista quando um usuário deseja informações sobre trabalhos abertos na empresa. 

    ![Captura de ecrã de compreensão de idiomas (LUIS) nova intenção caixa de diálogo](media/luis-quickstart-intents-only/create-intent.png "captura de ecrã de compreensão de idiomas (LUIS) intenção caixa de diálogo Novo")

1. Selecione **Done** (Concluído).

2. Adicione vários exemplos de declarações a essa intenção que você espera que um usuário pergunte:

    | Expressões de exemplo|
    |--|
    |Novos empregos publicados hoje?|
    |Existem novas vagas no escritório de Seattle?|
    |Há algum trabalho remoto ou trabalhos de telecomutação abertos para engenheiros?|
    |Existe algum trabalho em bases de dados?|
    |Estou procurando uma situação de cotrabalho no escritório de tampa.|
    |Há um estágio no escritório de San Francisco?|
    |Há algum trabalho em tempo de parte para as pessoas na faculdade?|
    |Procuro um novo emprego com responsabilidades em contabilidade|
    |Procurando um trabalho na cidade de Nova York para os alto-falantes bilíngües.|
    |Procurando uma nova situação com responsabilidades em contabilidade.|
    |Novos empregos?|
    |Mostre-me todos os trabalhos para engenheiros que foram adicionados nos últimos dois dias.|
    |Lançamentos de trabalho de hoje?|
    |Quais posições de contabilidade estão abertas no escritório de Londres?|
    |Que vagas estão disponíveis para Engenheiros Sénior?|
    |Onde posso encontrar as listas de empregos|

    [![Captura de ecrã da introdução de expressões com novos para MyStore intenção](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "captura de ecrã da introdução de expressões com novos para MyStore intenção")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

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

    O `verbose=true` parâmetro QueryString significa incluir **todas as tentativas** nos resultados da consulta do aplicativo. A matriz de entidades está vazia porque esta aplicação não tem entidades atualmente. 

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
    |Preencher a candidatura para o emprego 123456|
    |Eis o meu CV para a vaga 654234|
    |Aqui está meu currículo para a postagem do recepcionista em tempo parcial.|
    |Estou aplicando o trabalho de arte com essa documentação.|
    |Estou aplicando para o estágio da faculdade de verão em pesquisa e desenvolvimento em San Diego|
    |Estou solicitando que eu envie meu currículo para a posição temporária na lanchonete.|
    |Estou enviando meu currículo para a nova equipe de Autocar no Columbus, Ah|
    |Quero candidatar-me ao novo trabalho de contabilidade|
    |O trabalho 456789 contabilidade estágio papelada está aqui|
    |Emprego 567890 e a minha documentação|
    |Meus documentos para o estágio de contabilidade Tulsa são anexados.|
    |Minha papelada para a posição de entrega de férias|
    |Envie meu currículo para o novo trabalho de contabilidade em Seattle|
    |Submeter currículo para a vaga de engenharia|
    |Este é o meu CV. para o post 234123 em tampa.|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

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
