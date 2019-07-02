---
title: Ativar multi conversas
titleSuffix: Azure Cognitive Services
description: Instruções de utilização e o contexto para gerenciar o folheio vários, conhecido como multi por sua vez, para o bot de uma pergunta para outro. Ativar multi é a capacidade de ter uma conversa back lado para o outro em que o contexto à pergunta anterior influencia a próxima pergunta e resposta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508135"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Instruções de acompanhamento de utilização para criar vários folheio de uma conversa

Usar prompts de acompanhamento e contexto para gerenciar o folheio vários, conhecido como _ativar Multi_, para o bot de uma pergunta para outro.

Para ver como funciona a ativar multi, ver o vídeo de demonstração seguinte:

[![Ativar multi conversa em QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa de ativar multi?

Algumas perguntas não podem ser respondidas numa única vez. Ao conceber as conversas de (chatbot) da aplicação de cliente, um utilizador pode fazer uma pergunta que tem de ser filtrados ou refinados para determinar a resposta correta. Tornar este fluxo através de perguntas possível, apresentando o utilizador com o *seguimento solicita*.

Quando um usuário faz uma pergunta, o QnA Maker retorna a resposta _e_ quaisquer pedidos de acompanhamento. Esta resposta permite-lhe apresentar as perguntas de seguimento como opções. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversação de ativar multi de exemplo com o bot de bate-papo

Com o multi por sua vez, um chatbot gerencia uma conversa com um utilizador para determinar a resposta final, conforme mostrado na imagem seguinte:

![Uma caixa de diálogo ativar multi com avisos que orientar um usuário por meio de uma conversa](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, um usuário iniciou uma conversação introduzindo **minha conta**. A base de dados de conhecimento tem três pares de perguntas e respostas ligados. Para refinar a resposta, o usuário seleciona uma destas três opções na base de dados de conhecimento. A pergunta (n. º 1), tem três pedidos acompanhamento, o que são apresentados no bot de bate-papo como três opções (2). 

Quando o usuário seleciona uma opção (3), é apresentada a lista seguinte de refinar opções (n. º 4). Esta sequência continua (5) até que o utilizador determina a resposta final correta (#6).

> [!NOTE]
> Na imagem anterior, o **permitem ativar Multi** tiver sido selecionada a caixa de verificação para garantir que os avisos são apresentados. 

### <a name="use-multi-turn-in-a-bot"></a>Utilizar multi ativar num bot

Para gerir a conversa contextual, altere a sua aplicação de cliente por [adicionar código ao seu bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Adicionar o código permite aos utilizadores ver as instruções.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Criar uma conversação de ativar multi da estrutura de um documento

Quando cria uma base de dados de conhecimento, o **preencher sua BDC** secção apresenta um **permitem ativar multi extração a partir de URLs, ficheiros. pdf ou. docx** caixa de verificação. 

![Caixa de verificação para permitir a extração de ativar multi](../media/conversational-context/enable-multi-turn.png)

Quando seleciona esta opção para um documento importado, a conversa de ativar multi pode ser implícitas da estrutura do documento. Se existe essa estrutura, QnA Maker cria linha de comandos da acompanhamento que perguntas de pares e respostas para, como parte do processo de importação. 

Estrutura de folheio de multi pode ser inferida apenas a partir de URLs, ficheiros PDF, ou os ficheiros DOCX. Para obter um exemplo de estrutura, ver uma imagem de um [ficheiro manual de PDF do Microsoft Surface utilizador](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). Devido ao tamanho deste ficheiro PDF, o recurso do QnA Maker requer uma **pesquisa escalão de preço** dos **B** (15 índices) ou superior. 

![! [Exemplo de estrutura de um manual de utilizador] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Quando importa o documento PDF, o QnA Maker determina seguimento solicita da estrutura de criar o fluxo de conversação. 

1. No Criador de FAQ, selecione **criar uma base de dados de conhecimento**.
1. Criar ou utilizar um serviço existente do QnA Maker. No exemplo anterior do Microsoft Surface, uma vez que o ficheiro PDF é demasiado grande para um escalão mais baixo, utilizar um serviço QnA Maker com um **serviço de pesquisa** dos **B** (15 índices) ou superior.
1. Introduza um nome para a sua base de dados de conhecimento, tal como **manual superfície**.
1. Selecione o **permitem ativar multi extração a partir de URLs, ficheiros. pdf ou. docx** caixa de verificação. 
1. Selecione o URL manual superfície, **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Selecione o **criar a sua BDC** botão. 

    Depois de criar a base de dados de conhecimento, é apresentada uma vista dos pares de perguntas e respostas.

## <a name="show-questions-and-answers-with-context"></a>Mostrar as perguntas e respostas com o contexto

Reduza os pares de perguntas e respostas apresentados para apenas as pessoas com as conversas contextuais. 

Selecione **ver opções**e, em seguida, selecione **Show contexto (pré-visualização)** . A lista apresenta os pares de perguntas e respostas que contêm prompts de acompanhamento. 

![Filtrar pares de perguntas e respostas de conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

O contexto de ativar multi é apresentado na primeira coluna.

![! [A coluna "contexto (pré-visualização)"] (.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Na imagem anterior, **#1** indica o texto em negrito na coluna, o que significa a pergunta atual. A questão principal é o item superior na linha. Perguntas abaixo dele são os pares de perguntas e respostas ligados. Estes itens são selecionáveis, para que imediatamente pode ir para os outros itens de contexto. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicionar um par de perguntas e respostas existente como uma linha de comandos de acompanhamento

A pergunta original, **minha conta**, tem pedidos de acompanhamento, como **contas e iniciar sessão**. 

![A "Contas e iniciar sessão" respostas e solicitações de acompanhamento](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Adicione uma linha de comandos de seguimento necessária para um par de perguntas e respostas existente que não está atualmente associado. Uma vez que a pergunta não está vinculada a qualquer par de perguntas e respostas, a definição de vista atual precisa ser alterada.

1. Para ligar um par de perguntas e respostas existente como uma linha de comandos de acompanhamento, selecione a linha do par de perguntas e respostas. Para o manual de superfície, procure **terminar sessão** para reduzir a lista.
1. Na linha de **fim de sessão**, na **resposta** coluna, selecione **prompt de acompanhamento de adicionar**.
1. Os campos na **linha de comandos de seguimento (pré-visualização)** janela pop-up, introduza os seguintes valores:

    |Campo|Value|
    |--|--|
    |Apresenta o texto|Introduza **desligue o dispositivo**. Este é o texto personalizado para apresentar no prompt de acompanhamento.|
    |Só de contexto| Selecione esta caixa de verificação. Uma resposta é devolvida apenas se a pergunta Especifica o contexto.|
    |Ligação para responder|Introduza **utilizar o ecrã de início de sessão** para localizar o par de perguntas e respostas existente.|


1.  Uma correspondência é devolvida. Selecione esta resposta como o seguimento e, em seguida, selecione **guardar**. 

    ![A página de "Prompt de seguimento (pré-visualização)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Depois de adicionar a linha de acompanhamento, selecione **guardar e treinar** no painel de navegação superior.
  
### <a name="edit-the-display-text"></a>Editar o texto de exibição 

Quando é criada uma linha de comandos de acompanhamento e um par de perguntas e respostas existente é introduzido como o **Link para resposta**, pode introduzir novos **exibir texto**. Este texto não substitui a pergunta existente e não adiciona uma nova pergunta alternativa. Ele está separado desses valores. 

1. Para editar o texto de exibição, procure e selecione a pergunta na **contexto** campo.
1. Na linha para essa pergunta, selecione a linha de seguimento a coluna de resposta. 
1. Selecione o texto de exibição que pretende editar e, em seguida, selecione **editar**.

    ![O comando de edição para o texto de exibição](../media/conversational-context/edit-existing-display-text.png)

1. Na **prompt de seguimento** janela pop-up, alterar o texto de exibição existente. 
1. Quando terminar o texto de exibição de edição, selecione **guardar**. 
1. Na barra de navegação superior, **guardar e treinar**.


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicionar um novo par de perguntas e respostas como uma linha de comandos de acompanhamento

Quando adiciona um novo par de perguntas e respostas para a base de dados de conhecimento, cada par deve ser ligada a uma pergunta existente como uma linha de comandos de acompanhamento.

1. Na barra de ferramentas de base de dados de conhecimento, procure e selecione o par de perguntas e respostas existente para **contas e iniciar sessão**. 

1. Na **resposta** coluna para essa pergunta, selecione **linha de comandos de acompanhamento de adicionar**. 
1. Sob **linha de comandos de seguimento (pré-visualização)** , criar uma nova linha de seguimento ao introduzir os seguintes valores: 

    |Campo|Value|
    |--|--|
    |Apresenta o texto|*Criar uma conta do Windows*. O texto personalizado para apresentar no prompt de acompanhamento.|
    |Só de contexto|Selecione esta caixa de verificação. Esta resposta é devolvida apenas se a pergunta Especifica o contexto.|
    |Ligação para responder|Introduza o seguinte texto como a resposta:<br>*[Crie](https://account.microsoft.com/) uma conta do Windows com uma conta de e-mail novo ou existente*.<br>Quando salva e preparar a base de dados, esse texto será convertido. |
    |||

    ![Criar uma nova linha de comandos pergunta e resposta](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selecione **criar novo**e, em seguida, selecione **guardar**. 

    Esta ação cria um novo par de perguntas e respostas e os links a pergunta selecionada como uma linha de comandos de acompanhamento. O **contexto** coluna, para ambas as perguntas, indica uma relação de linha de comandos de acompanhamento. 

1. Selecione **ver opções**e, em seguida, selecione [ **Show contexto (pré-visualização)** ](#show-questions-and-answers-with-context).

    A nova pergunta mostra como ele será vinculado.

    ![Criar uma nova linha de acompanhamento](../media/conversational-context/new-qna-follow-up-prompt.png)

    A pergunta principal apresenta uma nova pergunta como uma das suas opções.

    ![! [A coluna de contexto, para ambas as perguntas, indica uma relação de linha de comandos de seguimento] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Depois de adicionar a linha de acompanhamento, selecione **guardar e treinar** na barra de navegação superior.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Ativar multi ativar durante o teste de pedidos de acompanhamento

Quando testar a pergunta com seguimento pede-lhe a **testar** painel, selecione **permitem ativar Multi**e, em seguida, introduza a sua pergunta. A resposta inclui as instruções de acompanhamento.

![A resposta inclui as instruções de acompanhamento](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Se não ativar a multi vez, a resposta é devolvida mas seguimento pedidos não são devolvidos.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Um pedido JSON para devolver uma resposta inicial e pedidos de acompanhamento

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

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta inicial e pedidos de acompanhamento

A secção anterior solicitada uma resposta e todos os pedidos para acompanhamento **contas e iniciar sessão**. A resposta inclui as informações de linha de comandos, que está localizadas em *respostas [0] .context*e o texto a apresentar ao utilizador. 

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
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
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
                "prompts": []
            }
        }
    ]
}
```

O `prompts` matriz fornece o texto no `displayText` propriedade e o `qnaId` valor. Pode mostrar essas respostas à medida que as seguintes opções apresentadas na conversação do fluxo e, em seguida, enviar selecionado `qnaId` voltar ao QnA Maker no pedido seguinte. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Um pedido JSON para devolver uma resposta não inicial e pedidos de acompanhamento

Preencher o `context` objeto para incluir o contexto anterior.

O pedido do JSON seguinte, a pergunta atual é *utilização Windows Hello para iniciar sessão* e a pergunta anterior foi *contas e iniciar sessão*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta não inicial e pedidos de acompanhamento

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
                "prompts": []
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
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
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
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Consultar a base de dados de conhecimento com o ID do QnA Maker

Em resposta à pergunta inicial, quaisquer pedidos de acompanhamento e está associado `qnaId` é devolvido. Agora que tem o ID, pode passar isso no corpo de pedido do prompt de acompanhamento. Se o corpo do pedido contém o `qnaId`e o objeto de contexto (que contém as propriedades do QnA Maker anteriores), em seguida, GenerateAnswer irá devolver a pergunta exata por ID, em vez de usar o algoritmo de classificação para encontrar a resposta, o texto da pergunta. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Apresentar pedidos e enviar o contexto no aplicativo cliente 

Adicionou instruções na sua base de dados de conhecimento e testou o fluxo no painel de teste. Agora tem de utilizar esses prompts no aplicativo cliente. Para o Bot Framework, as instruções não são apresentadas automaticamente nas aplicações cliente. Pode exibir as instruções como ações sugeridas ou botões como parte da resposta à consulta do usuário em aplicativos cliente, incluindo isso [exemplo de Bot Framework](https://aka.ms/qnamakermultiturnsample) em seu código. O aplicativo cliente deve armazenar o QnA Maker ID atual e a consulta de utilizador e passá-los no [objeto de contexto da GenerateAnswer API](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) para a seguinte consulta do utilizador. 

## <a name="display-order-is-supported-in-the-update-api"></a>Ordem de exibição é suportada na API de atualização

O [exibir texto e a ordem de exibição](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), devolvido na resposta JSON, que é suportado para edição pela [API de atualização](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Criar base de dados de conhecimento com pedidos de ativar multi com a API criar

Pode criar um caso de dados de conhecimento com pedidos de ativar multi com o [criar API do QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). A adicionar as instruções no `context` da propriedade `prompts` matriz. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Adicionar ou eliminar ativar multi é exibida com a API de atualização

Pode adicionar ou eliminar os avisos de ativar multi com o [API do QnA Maker atualização](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  A adicionar as instruções no `context` da propriedade `promptsToAdd` matriz e o `promptsToDelete` matriz. 


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre conversações contextuais nesta [exemplo de caixa de diálogo](https://aka.ms/qnamakermultiturnsample) ou Saiba mais sobre [bot conceitual de design para ativar multi conversas](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
