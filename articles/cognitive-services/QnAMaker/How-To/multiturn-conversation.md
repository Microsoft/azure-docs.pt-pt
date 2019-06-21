---
title: Ativar multi conversas
titleSuffix: Azure Cognitive Services
description: Instruções de utilização e o contexto para gerenciar o folheio vários, conhecido como multi por sua vez, para o bot de uma pergunta para outro. Ativar multi é a capacidade de ter uma cópia de e para trás conversa em que o contexto à pergunta anterior influencia a próxima pergunta e resposta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 1e46c935d298f2fe7ebfa4bce471288c9ab8a606
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271945"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Instruções de acompanhamento de utilização para criar vários folheio de uma conversa

Usar prompts de acompanhamento e contexto para gerenciar o folheio vários, conhecido como _ativar Multi_, para o bot de uma pergunta para outro.

Veja o seguinte vídeo de demonstração para ver como isso é feito.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa de ativar multi?

Algumas perguntas não podem ser respondidas numa única vez. Ao conceber as conversas de (chatbot) da aplicação de cliente, um utilizador pode fazer uma pergunta que tem de ser filtrados ou refinados para determinar a resposta correta. Este fluxo pelas perguntas é possível ao apresentar o utilizador com o **seguimento solicita**.

Quando o usuário faz a pergunta, o QnA Maker retorna a resposta _e_ quaisquer pedidos de acompanhamento. Isto permite-lhe apresentar as perguntas de seguimento como opções. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversação de ativar multi de exemplo com o bot de bate-papo

Um chatbot gerencia a conversação com o usuário pergunta a pergunta, para determinar a resposta final.

![Dentro do fluxo de conversação, gerir o estado de conversação num sistema de caixa de diálogo ativar multi ao fornecer avisos dentro as respostas, apresentadas como opções para continuar a conversa.](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, o utilizador introduziu `My account`. A base de dados de conhecimento tem 3 pares de QnA ligados. O utilizador tem de selecionar a partir de uma destas três opções para refinar a resposta. Na base de dados de conhecimento, a pergunta (n. º 1), tem três pedidos de acompanhamento, apresentados no bot de bate-papo como três opções (2). 

Quando o usuário seleciona uma opção (3), em seguida, é apresentada a lista seguinte de refinar opções (n. º 4). Isso pode continuar (5) até que a resposta correta e última (n º 6) é determinada.

A imagem anterior tiver **permitem ativar Multi** selecionada para apresentados avisos. 

### <a name="using-multi-turn-in-a-bot"></a>Usando o folheio multi num bot

Terá de alterar a aplicação de cliente para gerir a conversa contextual. Terá de adicionar [código para o seu bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting) para ver as instruções.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Criar uma conversação de ativar multi da estrutura de um documento

Quando cria uma base de dados de conhecimento, verá uma caixa de seleção opcional para ativar a extração de ativar multi. 

![Quando cria uma base de dados de conhecimento, verá uma caixa de seleção opcional para ativar a extração de ativar multi.](../media/conversational-context/enable-multi-turn.png)

Se selecionar esta opção, quando importa um documento, a conversa de ativar multi pode ser implícitas da estrutura de. Se existir essa estrutura, o QnA Maker cria os seguimento QnA pares de linha de comandos para. 

Estrutura de folheio de multi só pode ser inferida a partir de URLs, PDF ou DOCX ficheiros. 

A imagem seguinte de um Microsoft Surface [ficheiro PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) se destina a ser utilizado como um manual. Devido ao tamanho deste ficheiro PDF, o recurso de criador de FAQ do Azure requer a pesquisa de preços do escalão de B (15 índices) ou superior. 

![! [Se importar um documento, contextual conversação pode ser implícitas da estrutura de. Se existe essa estrutura, a ferramenta QnA Maker cria os pares de linha de comandos QnA acompanhamento para, como parte da importação de documento.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Ao importar o documento PDF, o QnA Maker determina prompts de acompanhamento de estrutura para criar o fluxo de conversação. 

1. Na **passo 1**, selecione **criar uma base de dados de conhecimento** no painel de navegação superior.
1. Na **passo 2**, criar ou utilizar um serviço QnA existente. Certifique-se de utilizar um serviço QnA com um serviço de pesquisa de B (15 índices) ou superior porque o ficheiro PDF do Manual de superfície é demasiado grande para um escalão mais baixo.
1. Na **passo 3**, introduza um nome para a sua base de dados de conhecimento, tal como `Surface manual`.
1. Na **passo 4**, selecione **permitem ativar multi extração a partir de URLs, ficheiros. pdf ou. docx.** Selecione o URL para a superfície Manual

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. Selecione o **criar a sua BDC** botão. 

    Uma vez criado o conhecimento, apresenta uma vista dos pares de perguntas e respostas.

## <a name="show-questions-and-answers-with-context"></a>Mostrar as perguntas e respostas com o contexto

Reduza os pares de perguntas e respostas apresentados aos apenas aqueles com conversas contextuais. 

1. Selecione **ver opções**, em seguida, selecione **Show contexto (pré-visualização)** . A lista mostra os pares de perguntas e respostas que contém instruções de acompanhamento. 

    ![Filtrar a pergunta e responder pares conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

2. Apresenta o contexto de ativar multi na primeira coluna.

    ![! [Ao importar o documento PDF, a ferramenta QnA Maker determina prompts de acompanhamento de estrutura para criar o fluxo de conversação. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    Na imagem anterior, #1 indica o texto em negrito na coluna, o que significa a pergunta atual. A questão principal é o item superior na linha. Perguntas abaixo são os pares de perguntas e respostas ligados. Estes itens são selecionáveis, para que possa imediatamente para os outros itens de contexto. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Adicionar o par de QnA existente como linha de acompanhamento

A pergunta original de `My account` tem instruções de seguimento como `Accounts and signing in`. 

![A pergunta original de "A minha conta" devolve corretamente a "Contas e iniciar sessão" responder e já possui as instruções de seguimento ligadas.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Adicione uma linha de comandos de seguimento necessária para um par de QnA existente que não está atualmente associado. Porque a pergunta não está vinculada a qualquer par de QnA, tem de alterar a definição de vista atual.

1. Para ligar um par de QnA existente como uma linha de comandos de acompanhamento, selecione a linha do par de perguntas e respostas. Para o manual de superfície, procure `Sign out` para reduzir a lista.
1. Na linha de `Signout`, selecione **linha de comandos de acompanhamento de Add** da **resposta** coluna.
1. Na **linha de comandos de seguimento (pré-visualização)** janela pop-up, introduza o seguinte:

    |Campo|Value|
    |--|--|
    |Apresenta o texto|`Turn off the device`. Este é o texto personalizado que escolher para apresentar no prompt de acompanhamento.|
    |Só de contexto|Selecionado. Esta resposta só será devolvida se a pergunta Especifica o contexto.|
    |Ligação para a resposta|Introduza `Use the sign-in screen` para localizar o par de QnA existente.|


1.  Uma correspondência é devolvida. Selecione esta resposta como o seguimento, em seguida, selecione **guardar**. 

    ![Ligação do prompt de seguimento à caixa de diálogo de resposta para uma resposta já existente, com o texto da resposta de pesquisa.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Depois de adicionar a linha de acompanhamento, não se esqueça de selecionar **guardar e treinar** no painel de navegação superior.
  
### <a name="edit-the-display-text"></a>Editar o texto de exibição 

Quando é criada uma linha de comandos de acompanhamento e um par de QnA existente está selecionado como o **Link para resposta**, pode introduzir novos **exibir texto**. Este texto não substitui a pergunta existente e não adiciona uma nova pergunta alternativa. Ele está separado desses valores. 

1. Para editar o texto de exibição, procure e selecione a pergunta na **contexto** campo.
1. Na linha essa pergunta, selecione a linha de acompanhamento na coluna de resposta. 
1. Selecione o texto de exibição que pretende editar, em seguida, selecione **editar**.

    ![Selecione o texto de exibição que pretende editar e, em seguida, selecione editar.](../media/conversational-context/edit-existing-display-text.png)

1. O **prompt de seguimento** pop-up permite-lhe alterar o texto de exibição existente. 
1. Quando tiver terminado o texto de exibição de edição, selecione **guardar**. 
1. Não se esqueça de selecionar **guardar e treinar** no painel de navegação superior.


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

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Adicionar novo par de QnA como linha de acompanhamento

Adicione um novo par de QnA para a base de dados de conhecimento. O par de QnA deve ser ligado a uma pergunta existente como uma linha de comandos de acompanhamento.

1. Na barra de ferramentas da base de dados de conhecimento, procure e selecione o par de QnA existente para `Accounts and Signing In`. 

1. Na **resposta** coluna para essa pergunta, selecione **linha de comandos de acompanhamento de adicionar**. 
1. O **linha de comandos de seguimento (pré-visualização)** , criar uma nova linha de seguimento ao introduzir os seguintes valores: 

    |Campo de texto|Value|
    |--|--|
    |**Apresenta o texto**|`Create a Windows Account`. Este é o texto personalizado que escolher para apresentar no prompt de acompanhamento.|
    |**Context-only**|Selecionado. Esta resposta só será devolvida se a pergunta Especifica o contexto.|
    |**Ligação para responder**|Introduza o seguinte texto como a resposta:<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>Quando salva e preparar a base de dados, este texto é convertido |
    |||

    ![Criar novo QnA de linha de comandos](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selecione **criar novo** , em seguida, selecione **guardar**. 

    Isso criou um novo par de perguntas e respostas e ligado à pergunta selecionada como uma linha de comandos de acompanhamento. O **contexto** coluna, para ambas as perguntas, indica uma relação de linha de comandos de acompanhamento. 

1. Alteração da **ver opções** para [Mostrar contexto](#show-questions-and-answers-with-context).

    A nova pergunta mostra como ele será vinculado.

    ![Criar uma nova linha de acompanhamento ](../media/conversational-context/new-qna-follow-up-prompt.png)

    A questão principal é apresentado à nova pergunta como uma das suas opções.

    ![! [A coluna de contexto, para ambas as perguntas, indica uma relação de linha de comandos de acompanhamento.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Depois de adicionar a linha de acompanhamento, não se esqueça de selecionar **guardar e treinar** no painel de navegação superior.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>Ativar multi mão quando solicita o acompanhamento de teste

Quando testar a pergunta com seguimento pedidos na **teste** painel, selecione **permitem ativar Multi**e introduza a sua pergunta. A resposta inclui as instruções de acompanhamento.

![Ao testar a pergunta no painel de teste, a resposta inclui as instruções de acompanhamento.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Se não ativar a multi vez, a resposta será retornada mas seguimento pedidos não são devolvidos.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Pedido JSON para devolver a resposta inicial e pedidos de acompanhamento

Utilizar vazio `context` objeto para pedir a resposta à pergunta do usuário e incluir instruções de acompanhamento. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
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
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "promptsToAdd": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ],
                "promptsToDelete":[]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "promptsToAdd": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ],
                "promptsToDelete":[]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "promptsToAdd": [],
                "promptsToDelete":[]
            }
        }
    ]
}
```

O `promptsToAdd` matriz fornece o texto no `displayText` propriedade e o `qnaId` valor para que pode mostrar essas respostas como seguintes opções apresentadas na conversação do fluxo, em seguida, enviar o valor selecionado para a ferramenta QnA Maker no pedido seguinte. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Pedido JSON para devolver a resposta não inicial e pedidos de acompanhamento

Preencher o `context` objeto para incluir o contexto anterior.

O pedido do JSON seguinte, a pergunta atual é `Use Windows Hello to sign in` e a pergunta anterior foi `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
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
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "promptsToAdd": [],
                "promptsToDelete":[]
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "promptsToAdd": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ],
                "promptsToDelete":[]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "promptsToAdd": [],
                "promptsToDelete":[]
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>Consultar a base de dados de conhecimento com o ID do QnA

Em resposta à pergunta inicial, quaisquer pedidos de acompanhamento e está associado `qnaId` é devolvido. Agora que tem o ID, pode passar isso no corpo de pedido do prompt de acompanhamento. Se o corpo do pedido contém o `qnaId`e o objeto de contexto (que contém as propriedades de QnA anteriores), em seguida, GenerateAnswer irá devolver a pergunta exata por ID, em vez de usar o algoritmo de classificação para encontrar a resposta, o texto da pergunta. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Exibindo prompts e enviar o contexto no aplicativo cliente 

Adicionou instruções na sua base de dados de conhecimento e testou o fluxo no painel de teste. Agora tem de utilizar esses prompts no aplicativo cliente. Para o Bot Framework, as instruções não serão automaticamente iniciado a aparecer nas aplicações cliente. Pode mostrar as instruções como ações sugeridas ou botões como parte da resposta à consulta do utilizador num cliente de aplicativos, incluindo isso [exemplo de Bot Framework](https://aka.ms/qnamakermultiturnsample) em seu código. O aplicativo cliente deve armazenar o ID de QnA atual e a consulta de utilizador e passá-los no [objeto de contexto da GenerateAnswer API](#json-request-to-return-non-initial-answer-and-follow-up-prompts) para a seguinte consulta do utilizador. 

## <a name="display-order-supported-in-api"></a>Ordem de exibição suportada na API

O [exibir texto e a ordem de exibição](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), devolvido na resposta JSON, que é suportado para edição pela [API de atualização](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre conversações contextuais do [exemplo de caixa de diálogo](https://aka.ms/qnamakermultiturnsample) ou Saiba mais [bot conceitual de design para ativar multi conversas](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
