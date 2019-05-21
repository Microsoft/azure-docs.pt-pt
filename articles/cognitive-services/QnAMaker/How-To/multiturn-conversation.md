---
title: Ativar multi conversas
titleSuffix: Azure Cognitive Services
description: Instruções de utilização e o contexto para gerenciar o folheio vários, conhecido como multi por sua vez, para o bot de uma pergunta para outro. Ativar multi é a capacidade de ter uma cópia de e para trás conversa em que o contexto à pergunta anterior influencia a próxima pergunta e resposta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/14/2019
ms.author: diberry
ms.openlocfilehash: f12b55e9b00e933e13f84832b8cc36267a1da05f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954859"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Instruções de acompanhamento de utilização para criar vários folheio de uma conversa

Usar prompts de acompanhamento e contexto para gerenciar o folheio vários, conhecido como _ativar Multi_, para o bot de uma pergunta para outro.

Aprenda com o [vídeo de demonstração](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa de ativar multi?

Alguns tipos de conversação não podem ser concluídos numa única vez. Ao conceber as conversas de (chatbot) da aplicação de cliente, um utilizador pode fazer uma pergunta que tem de ser filtrados ou refinados para determinar a resposta correta. Este fluxo pelas perguntas é possível ao apresentar o utilizador com o **seguimento solicita**.

Quando o usuário faz a pergunta, o QnA Maker retorna a resposta _e_ quaisquer pedidos de acompanhamento. Isto permite-lhe apresentar as perguntas de seguimento como opções. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversação de ativar multi de exemplo com o bot de bate-papo

Um chatbot gerencia a conversa, pergunta a pergunta, com o utilizador para determinar a resposta final.

![Dentro do fluxo de conversação, gerir o estado de conversação num sistema de caixa de diálogo ativar multi ao fornecer avisos dentro as respostas, apresentadas como opções para continuar a conversa.](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, a pergunta do utilizador tem de ser refinado antes de retornar a resposta. Na base de dados de conhecimento, a pergunta (n. º 1), tem quatro prompts de acompanhamento, apresentadas no bot de bate-papo como quatro opções (2). 

Quando o usuário seleciona uma opção (3), em seguida, é apresentada a lista seguinte de refinar opções (n. º 4). Isso pode continuar (5) até que a resposta correta e última (n º 6) é determinada.

Terá de alterar a aplicação de cliente para gerir a conversa contextual.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>Criar uma conversação de ativar multi da estrutura do documento
Quando cria uma base de dados de conhecimento, verá uma caixa de seleção opcional para ativar a extração de ativar multi. Se selecionar esta opção, quando importa um documento, a conversa de ativar multi pode ser implícitas da estrutura de. Se existir essa estrutura, o QnA Maker cria os seguimento QnA pares de linha de comandos para. Estrutura de folheio de multi só pode ser inferida a partir de URLs, PDF ou DOCX ficheiros. 

A imagem seguinte de um Microsoft Surface [ficheiro PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) se destina a ser utilizado como um manual. 

![! [Se importar um documento, contextual conversação pode ser implícitas da estrutura de. Se existe essa estrutura, a ferramenta QnA Maker cria os pares de linha de comandos QnA acompanhamento para, como parte da importação de documento.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Ao importar o documento PDF, o QnA Maker determina prompts de acompanhamento de estrutura para criar o fluxo de conversação. 

![! [Ao importar o documento PDF, a ferramenta QnA Maker determina prompts de acompanhamento de estrutura para criar o fluxo de conversação. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="show-questions-and-answers-with-context"></a>Mostrar as perguntas e respostas com o contexto

1. Reduza os pares de perguntas e respostas apresentados aos apenas aqueles com conversas contextuais. Selecione **ver opções**, em seguida, selecione **Show contexto (pré-visualização)**. A lista estará vazia, até que adicione o par de pergunta e resposta primeiro com uma linha de comandos de acompanhamento. 

    ![Filtrar a pergunta e responder pares conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Adicionar novo par de QnA como linha de acompanhamento

1. Selecione **QnA adicionar par**. 
1. Introduzir novo texto da pergunta, `Give feedback.` com uma resposta de `What kind of feedback do you have?`.

1. Na **resposta** coluna para essa pergunta, selecione **linha de comandos de acompanhamento de adicionar**. 
1. O **linha de comandos de seguimento (pré-visualização)** janela pop-up, pode procurar uma pergunta existente ou introduza uma nova pergunta. Crie uma nova linha ao introduzir os seguintes valores: 

    |Campo de texto|Value|
    |--|--|
    |**Apresenta o texto**|`Feedback on an QnA Maker service`|
    |**Ligação para responder**|`How would you rate QnA Maker??`|
    |||

    ![Criar novo QnA de linha de comandos](../media/conversational-context/create-child-prompt-from-parent.png)

1. Verifique **só de contexto**. O **só de contexto** opção indica que este texto do utilizador será compreendido _apenas_ se for indicado em resposta à pergunta anterior. Para este cenário, o texto de aviso não faz qualquer sentido como uma pergunta autónomo, apenas faz sentido no contexto da pergunta anterior.
1. Selecione **criar novo** , em seguida, selecione **guardar**. 

    Isso criou um novo par de perguntas e respostas e ligado à pergunta selecionada como uma linha de comandos de acompanhamento. O **contexto** coluna, para ambas as perguntas, indica uma relação de linha de comandos de acompanhamento. 

    ![! [A coluna de contexto, para ambas as perguntas, indica uma relação de linha de comandos de acompanhamento.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Selecione **Adicionar linha de seguimento** para o `Give feedback` pergunta para adicionar outro prompt de acompanhamento. Esta ação abre o **linha de comandos de seguimento (pré-visualização)** janela pop-up.

1. Crie uma nova linha ao introduzir os seguintes valores:

    |Campo de texto|Value|
    |--|--|
    |**Apresenta o texto**|`Feedback on an existing feature`|
    |**Ligação para responder**|`Which feature would you like to give feedback on?`|
    |||

1. Verifique **só de contexto**. O **só de contexto** opção indica que este texto do utilizador será compreendido _apenas_ se for indicado em resposta à pergunta anterior. Para este cenário, o texto de aviso não faz qualquer sentido como uma pergunta autónomo, apenas faz sentido no contexto da pergunta anterior.

1. Selecione **Guardar**. 

    Isso criou uma nova pergunta e ligado a pergunta como uma pergunta de linha de comandos de acompanhamento para o `Give feedback` pergunta.
    
    Neste momento, a pergunta superior tem dois pedidos de seguimento gostos à pergunta anterior, `Give feedback`.

    ![! [Neste momento, a pergunta superior tem dois pedidos de seguimento gostos à pergunta anterior, "Comentários".] (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. Selecione **guardar e treinar** para preparar a base de dados de conhecimento com as perguntas de novo. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Adicionar o par de QnA existente como linha de acompanhamento

1. Se deseja vincular um par de QnA existente como uma linha de comandos de acompanhamento, selecione a linha do par de perguntas e respostas.
1. Selecione **linha de comandos de acompanhamento de adicionar** nessa linha.
1. Na **linha de comandos de seguimento (pré-visualização)** janela pop-up, introduza o texto de resposta na caixa de pesquisa. Todas as correspondências são devolvidas. Selecione a resposta desejado como o seguimento e verifique **só de contexto**, em seguida, selecione **guardar**. 

    ![Ligação do prompt de seguimento à caixa de diálogo de resposta para uma resposta já existente, com o texto da resposta de pesquisa.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

    Depois de adicionar a linha de acompanhamento, não se esqueça de selecionar **guardar e treinar**.
  
<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>Teste que o QnA definidos para obter todos os seguimento pede-lhe

Quando testar a pergunta com seguimento pedidos na **teste** painel, selecione **permitem ativar Multi**e introduza a sua pergunta. A resposta inclui as instruções de acompanhamento.

![Ao testar a pergunta no painel de teste, a resposta inclui as instruções de acompanhamento.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Pedido JSON para devolver a resposta inicial e pedidos de acompanhamento

Utilizar vazio `context` objeto para pedir a resposta à pergunta do usuário e incluir instruções de acompanhamento. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>Resposta JSON para retornar a resposta inicial e pedidos de acompanhamento

A secção anterior pediu uma resposta e de quaisquer pedidos de seguimento necessária para `Accounts and signing in`. A resposta inclui as informações de linha de comandos, localizadas em `answers[0].context`, inclua o texto a apresentar ao utilizador. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way they like. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

O `prompts` matriz fornece o texto no `displayText` propriedade e o `qnaId` valor para que pode mostrar essas respostas como opções apresentadas seguintes no fluxo de conversação. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Pedido JSON para devolver a resposta não inicial e pedidos de acompanhamento

Preencher o `context` objeto para incluir o contexto anterior.

O pedido do JSON seguinte, a pergunta atual é `Use Windows Hello to sign in` e a pergunta anterior foi `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>Resposta JSON para retornar a resposta não inicial e pedidos de acompanhamento

O QnA Maker _GenerateAnswer_ resposta JSON inclui as instruções de acompanhamento no `context` propriedade do primeiro item a `answers` objeto:

```JSON
{
    "answers": [
        {
            "questions": [
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Exibindo prompts e enviar o contexto no aplicativo cliente 

Se adicionou os prompts na sua base de dados de conhecimento e testou o fluxo no painel de teste, as instruções não serão automaticamente iniciado a aparecer nas aplicações cliente. Pode mostrar as instruções como ações sugeridas ou botões como parte da resposta à consulta do utilizador num cliente de aplicativos, incluindo isso [exemplo de Bot Framework](https://aka.ms/qnamakermultiturnsample) em seu código. O aplicativo cliente deve armazenar o ID de QnA atual e a consulta de utilizador e passá-los no [objeto de contexto da GenerateAnswer API](#json-request-to-return-non-initial-answer-and-follow-up-prompts) para a seguinte consulta do utilizador.

## <a name="display-order-supported-in-api"></a>Ordem de exibição suportada na API

A ordem de apresentação, devolvida na resposta JSON, é suportada para edição por apenas a API. 

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre conversações contextuais do [exemplo de caixa de diálogo](https://aka.ms/qnamakermultiturnsample) ou Saiba mais [bot conceitual de design para ativar multi conversas](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
