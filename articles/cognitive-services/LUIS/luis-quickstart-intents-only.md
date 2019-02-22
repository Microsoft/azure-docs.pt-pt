---
title: Prever intenções
titleSuffix: Azure Cognitive Services
description: Neste tutorial, crie uma aplicação personalizada que prevê a intenção de um utilizador. Esta aplicação é o tipo mais simples de aplicação LUIS, porque não extrai vários elementos de dados do texto da expressão, como endereços de e-mail ou datas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 067829a1d9425ede1320242e364eca7c30bb7053
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593918"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Tutorial: Criar aplicação LUIS para determinar as intenções dos usuários

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

## <a name="user-intentions-as-intents"></a>Intenções dos usuários como intenções

É a finalidade da aplicação determinar a intenção de conversação, texto de linguagem natural: 

`Are there any new positions in the Seattle office?`

Estas intenções estão categorizadas em **Intenções**. 

Esta aplicação tem algumas intenções. 

|Intenção|Objetivo|
|--|--|
|ApplyForJob|Determine se o utilizador é aplicar para uma tarefa.|
|GetJobInformation|Determine se o utilizador está à procura de informações sobre tarefas em geral ou de uma tarefa específica.|
|Nenhuma|Determinar se o utilizador está fazendo algo aplicação deixar de responder. Este se intenção fornecido como parte da criação de aplicações e não pode ser eliminada. |

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Criar intenção de informações da tarefa

1. Selecione **Create new intent** (Criar nova intenção). Introduza o nome da nova intenção `GetJobInformation`. Este objetivo é prever quando um usuário deseja informações sobre as tarefas abertas na empresa. 

    ![Captura de ecrã de compreensão de idiomas (LUIS) nova intenção caixa de diálogo](media/luis-quickstart-intents-only/create-intent.png "captura de ecrã de compreensão de idiomas (LUIS) intenção caixa de diálogo Novo")

1. Selecione **Done** (Concluído).

2. Adicione várias expressões de exemplo para este objetivo que espera que um utilizador pedir:

    | Expressões de exemplo|
    |--|
    |Novos empregos publicados hoje?|
    |Existem novas vagas no escritório de Seattle?|
    |Existem qualquer função de trabalho remota ou trabalha à distância de tarefas aberto para engenheiros de?|
    |Existe algum trabalho em bases de dados?|
    |Estou procurando uma situação de colega de trabalho no escritório tampa.|
    |Existe um estágio no escritório de san francisco?|
    |Existe qualquer trabalho meio período para as pessoas na faculdade?|
    |Procuro um novo emprego com responsabilidades em contabilidade|
    |Procurando oradores bilingue para uma tarefa na cidade de nova york.|
    |À procura de uma situação de novo com responsabilidades na gestão de contas.|
    |Novos empregos?|
    |Mostre-me todos os trabalhos para os engenheiros que foram adicionados nos últimos 2 dias.|
    |Emprego de hoje?|
    |As posições de gestão de contas estão abertas no escritório de Londres?|
    |Que vagas estão disponíveis para Engenheiros Sénior?|
    |Onde posso encontrar as listas de empregos|

    [![Captura de ecrã da introdução de expressões com novos para MyStore intenção](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "captura de ecrã da introdução de expressões com novos para MyStore intenção")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Ao fornecer _expressões de exemplo_, são o treinamento LUIS sobre que tipos de expressões com deveriam ser previstos para esse objetivo. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar expressões de exemplo para a intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Preparar a aplicação antes de teste ou de publicação

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar a aplicação para consultar a partir do ponto final

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Obter previsão intenção do ponto final

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

    O `verbose=true` parâmetro de cadeia de consulta significa incluem **todas as intenções** nos resultados de consulta da aplicação. A matriz de entidades está vazia porque esta aplicação não tem entidades atualmente. 

    O resultado JSON identifica a intenção com a melhor classificação como a propriedade **`topScoringIntent`**. Todas as classificações estão compreendidas entre 1 e 0, estando a melhor classificação próxima de 1. 

## <a name="create-intent-for-job-applications"></a>Criar intenção para aplicativos de tarefa

Regresse ao portal do LUIS e criar uma intenção de novo para determinar se a expressão de utilizador é sobre como aplicar para uma tarefa.

1. Selecione **Build** (Criar) no menu direito superior para regressar à criação da aplicação.

1. Selecione **intenções** no menu à esquerda para obter a lista de objetivos.

1. Selecione **Create new intent** (Criar nova intenção) e introduza o nome `ApplyForJob`. 

    ![Caixa de diálogo do LUIS para criar a nova intenção](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Adicione várias expressões a esta intenção, que espera que um utilizador peça, tais como:

    | Expressões de exemplo|
    |--|
    |Preencher a candidatura para o emprego 123456|
    |Eis o meu CV para a vaga 654234|
    |Aqui está meu currículo para a postagem de rececionista atende meio período.|
    |Eu estou aplicando-se para a tarefa de suporte técnico de última geração com este papelada.|
    |Estou aplicando para o estágio de faculdade de Verão em pesquisa e desenvolvimento em San Diego|
    |Estou a pedir para enviar meu currículo para a posição temporário do restaurante.|
    |Estou a submeter o meu currículo para o novo agrupamento de Autocar no Columbus, AH|
    |Quero candidatar-me ao novo trabalho de contabilidade|
    |Papelada de estágio de gestão de contas de tarefa 456789 está aqui|
    |Emprego 567890 e a minha documentação|
    |Meus documentos para o estágio de contabilidade tulsa são anexados.|
    |Minha papelada para a posição de entrega de feriado|
    |Tente enviar meu currículo para a nova tarefa de gestão de contas em seattle|
    |Submeter currículo para a vaga de engenharia|
    |Este é o meu CV. para o post 234123 em Tampa.|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>Preparar novamente

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Publicar novamente

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Obter novamente a predição de intenção

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

## <a name="client-application-next-steps"></a>Passos seguintes da aplicação de cliente

Depois de devolver a resposta JSON, o LUIS conclui este pedido. O LUIS não fornece respostas às expressões do utilizador, apenas identifica o tipo de informação que está a ser pedida em linguagem natural. O seguimento conversacional é fornecido pela aplicação de cliente, como um Bot do Azure. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tipos de entidades](luis-concept-entity-types.md)
* [Como dar formação](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Passos Seguintes

Este tutorial criou a aplicação Recursos Humanos (RH), criou duas intenções, adicionou expressões de exemplo a cada intenção, adicionou expressões de exemplo à intenção None, preparou, publicou e testou no ponto final. Estes são os passos básicos de criação de um modelo do LUIS. 

Continuar com esta aplicação, [adicionando uma lista simple de entidade e a frase](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Adicionar intenções e entidades pré-concebidas a esta aplicação](luis-tutorial-prebuilt-intents-entities.md)
