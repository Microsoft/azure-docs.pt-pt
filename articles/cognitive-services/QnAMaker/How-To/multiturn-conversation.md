---
title: Conversas com vários desligamentos-QnA Maker
titleSuffix: Azure Cognitive Services
description: Use prompts e contexto para gerenciar as várias ativações, conhecidas como Multiturn, para o bot de uma pergunta para outra. A passagem múltipla é a capacidade de ter uma conversa de frente e para trás, na qual o contexto da pergunta anterior influencia a próxima pergunta e resposta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: dc99626e2341e180ba0ab191003cf3a6ba9b72e9
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695151"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Utilizar pedidos de seguimento para criar vários turnos numa conversa

Use os prompts e o contexto de acompanhamento para gerenciar os vários ativadores, conhecidos como _Multiturn_, para o bot de uma pergunta para outra.

Para ver como funciona a opção múltipla, veja o vídeo de demonstração a seguir:

[![Multi-ativar a conversa QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa com vários desligamentos?

Algumas perguntas não podem ser respondidas em uma única vez. Quando você projeta suas conversas de aplicativo cliente (bot de bate-papo), um usuário pode fazer uma pergunta que precisa ser filtrada ou refinada para determinar a resposta correta. Você pode fazer esse fluxo seguindo as perguntas possíveis apresentando o usuário com *avisos de acompanhamento*.

Quando um usuário faz uma pergunta, QnA Maker retorna a resposta _e_ quaisquer avisos de acompanhamento. Essa resposta permite que você apresente as perguntas de acompanhamento como opções. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exemplo de conversa múltipla com bot de chat

Com o multi-Transform, um bot de chat gerencia uma conversa com um usuário para determinar a resposta final, conforme mostrado na imagem a seguir:

![Uma caixa de diálogo de várias desligamentos com prompts que orientam um usuário por meio de uma conversa](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, um usuário iniciou uma conversa inserindo **minha conta**. A base de dados de conhecimento tem três pares de perguntas e respostas vinculados. Para refinar a resposta, o usuário seleciona uma das três opções na base de dados de conhecimento. A pergunta (#1), tem três prompts de acompanhamento, que são apresentados no bot de bate-papo como três opções (#2). 

Quando o usuário seleciona uma opção (#3), a próxima lista de opções de refinamento (#4) é apresentada. Essa sequência continua (#5) até que o usuário determine a resposta final correta (#6).

> [!NOTE]
> Na imagem anterior, a caixa de seleção **habilitar várias desativações** foi selecionada para garantir que os prompts sejam exibidos. 

### <a name="use-multi-turn-in-a-bot"></a>Usar o multi-Transform em um bot

Para gerenciar a conversa contextual, altere o aplicativo cliente [adicionando o código ao bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Adicionar o código permite que os usuários vejam os prompts.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Criar uma conversa de passagem múltipla da estrutura do documento

Quando você cria uma base de dados de conhecimento, a seção **popular a KB** exibe uma caixa **de seleção Habilitar extração múltipla de URLs, arquivos. pdf ou. docx** . 

![Caixa de seleção para habilitar a extração de vários desligamentos](../media/conversational-context/enable-multi-turn.png)

Quando você seleciona essa opção, a conversa de troca múltipla pode ser implícita na estrutura do documento. Se essa estrutura existir, QnA Maker criará o aviso de acompanhamento de perguntas e respostas de pares para você como parte do processo de importação. 

A estrutura de vários folheios pode ser inferida apenas de URLs, arquivos PDF ou arquivos DOCX. Para obter um exemplo de estrutura, exiba uma imagem de um [arquivo PDF manual do usuário do Microsoft Surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). Devido ao tamanho desse arquivo PDF, o recurso QnA Maker requer um tipo de **preço de pesquisa** de **B** (15 índices) ou superior. 

![! [Exemplo de estrutura em um manual do usuário] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="determine-multi-turn-structure-from-format"></a>Determinar a estrutura de vários folheios a partir do formato

QnA Maker determina a estrutura de vários folheios de:

* Tamanho da fonte do cabeçalho – se você usar estilo, cor ou algum outro mecanismo para implicar a estrutura em seu documento, QnA Maker não extrairá os prompts de várias ativações. 

As regras dos títulos incluem:

* Não termine um título com um ponto de interrogação, `?`. 

### <a name="add-file-with-multi-turn-prompts"></a>Adicionar arquivo com prompts de múltipla ativação

Quando você adiciona um documento de várias transformações, QnA Maker determina os prompts de acompanhamento da estrutura para criar o fluxo de conversação. 

1. Em QnA Maker, selecione uma base de dados de conhecimento existente que foi criada com **habilitar extração múltipla de URLs, arquivos. pdf ou. docx.** habilitado. 
1. Vá para a página **configurações** , selecione o arquivo ou a URL a ser adicionada. 
1. **Salve e treine** a base de dados de conhecimento.

> [!Caution]
> Não há suporte para o suporte para o uso de um arquivo base de conhecimento multiturns TSV ou XLS exportado como fonte de dados para uma base de conhecimento nova ou vazia. Você precisa **importar** esse tipo de arquivo, na página **configurações** do portal de QnA Maker, para adicionar prompts de multiativação exportados a uma base de dados de conhecimento.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Criar uma base de dados de conhecimento com prompts de múltipla ativação com a API de criação

Você pode criar um caso de conhecimento com prompts de múltipla ativação usando o [QnA Maker criar API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Os prompts estão adicionando na matriz `prompts` da propriedade `context`. 

## <a name="show-questions-and-answers-with-context"></a>Mostrar perguntas e respostas com contexto

Reduza os pares de perguntas e respostas exibidos para apenas aqueles com conversas contextuais. 

Selecione **Opções de exibição**e, em seguida, selecione **Mostrar contexto (versão prévia)** . A lista exibe os pares de perguntas e respostas que contêm avisos de acompanhamento. 

![Filtrar pares de perguntas e respostas por conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

O contexto de múltipla ativação é exibido na primeira coluna.

![! [A coluna "contexto (visualização)"] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Na imagem anterior, **#1** indica texto em negrito na coluna, que significa a pergunta atual. A pergunta pai é o item superior na linha. As perguntas abaixo são os pares de perguntas e respostas vinculadas. Esses itens são selecionáveis, para que você possa ir imediatamente para os outros itens de contexto. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicionar um par de perguntas e respostas existente como um aviso de acompanhamento

A pergunta original, **minha conta**, tem avisos de acompanhamento, como **contas e entrada**. 

![As respostas de "contas e entrada" e as solicitações de acompanhamento](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Adicione um aviso de acompanhamento a um par de perguntas e respostas existentes que não esteja vinculado no momento. Como a pergunta não está vinculada a nenhum par de perguntas e respostas, a configuração de exibição atual precisa ser alterada.

1. Para vincular um par de perguntas e respostas existente como um aviso de acompanhamento, selecione a linha para o par de perguntas e respostas. Para o manual da superfície, procure **sair** para reduzir a lista.
1. Na linha de **saída**, na coluna **resposta** , selecione **Adicionar aviso de acompanhamento**.
1. Nos campos na janela pop-up de **aviso de acompanhamento (visualização)** , insira os seguintes valores:

    |Campo|Value|
    |--|--|
    |Exibir texto|Digite desligar **o dispositivo**. Esse é o texto personalizado a ser exibido no prompt de acompanhamento.|
    |Somente contexto| Marque essa caixa de seleção. Uma resposta será retornada somente se a pergunta especificar o contexto.|
    |Link para responder|Digite **usar a tela de entrada** para localizar o par de perguntas e respostas existente.|


1.  Uma correspondência é retornada. Selecione essa resposta como o acompanhamento e, em seguida, selecione **salvar**. 

    ![A página "aviso de acompanhamento (visualização)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Depois de adicionar o prompt de acompanhamento, selecione **salvar e treinar** na navegação superior.
  
### <a name="edit-the-display-text"></a>Editar o texto de exibição 

Quando um prompt de acompanhamento é criado e um par de perguntas e respostas existente é inserido como o **link para responder**, você pode inserir o novo texto de **exibição**. Esse texto não substitui a pergunta existente e não adiciona uma nova pergunta alternativa. Ele é separado desses valores. 

1. Para editar o texto de exibição, procure e selecione a pergunta no campo **contexto** .
1. Na linha dessa pergunta, selecione o prompt de acompanhamento na coluna resposta. 
1. Selecione o texto de exibição que você deseja editar e, em seguida, selecione **Editar**.

    ![O comando Editar para o texto de exibição](../media/conversational-context/edit-existing-display-text.png)

1. Na janela pop-up de **aviso de acompanhamento** , altere o texto de exibição existente. 
1. Quando terminar de editar o texto de exibição, selecione **salvar**. 
1. Na barra de navegação superior, **salve e treine**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicionar um novo par de perguntas e respostas como um aviso de acompanhamento

Quando você adiciona um novo par de perguntas e respostas à base de dados de conhecimento, cada par deve ser vinculado a uma pergunta existente como um aviso de acompanhamento.

1. Na barra de ferramentas da base de dados de conhecimento, procure e selecione o par pergunta-e-resposta existente para **contas e entrada**. 

1. Na coluna **resposta** dessa pergunta, selecione **Adicionar aviso de acompanhamento**. 
1. Em **aviso de acompanhamento (versão prévia)** , crie um novo prompt de acompanhamento inserindo os seguintes valores: 

    |Campo|Value|
    |--|--|
    |Exibir texto|*Crie uma conta do Windows*. O texto personalizado a ser exibido no aviso de acompanhamento.|
    |Somente contexto|Marque essa caixa de seleção. Essa resposta será retornada somente se a pergunta especificar o contexto.|
    |Link para responder|Digite o seguinte texto como a resposta:<br>*[Crie](https://account.microsoft.com/) uma conta do Windows com uma conta de email nova ou existente*.<br>Quando você salvar e treinar o banco de dados, esse texto será convertido. |
    |||

    ![Criar uma nova pergunta e resposta de prompt](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selecione **criar novo**e, em seguida, selecione **salvar**. 

    Essa ação cria um novo par de perguntas e respostas e vincula a pergunta selecionada como um aviso de acompanhamento. A coluna de **contexto** , para as duas perguntas, indica uma relação de aviso de acompanhamento. 

1. Selecione **Opções de exibição**e, em seguida, selecione [**Mostrar contexto (versão prévia)** ](#show-questions-and-answers-with-context).

    A nova pergunta mostra como ele está vinculado.

    ![Criar um novo aviso de acompanhamento](../media/conversational-context/new-qna-follow-up-prompt.png)

    A pergunta pai exibe uma nova pergunta como uma de suas escolhas.

    ![! [A coluna de contexto, para as duas perguntas, indica uma relação de aviso de acompanhamento] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Depois de adicionar o prompt de acompanhamento, selecione **salvar e treinar** na barra de navegação superior.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Habilitar a troca múltipla durante o teste de avisos de acompanhamento

Ao testar a pergunta com os prompts de acompanhamento no painel de **teste** , selecione **habilitar a opção múltipla**e insira sua pergunta. A resposta inclui os prompts de acompanhamento.

![A resposta inclui os prompts de acompanhamento](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Se você não habilitar a opção múltipla, a resposta será retornada, mas os prompts de acompanhamento não serão retornados.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma solicitação JSON para retornar uma resposta inicial e prompts de acompanhamento

Use o objeto `context` vazio para solicitar a resposta à pergunta do usuário e incluir avisos de acompanhamento. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta inicial e prompts de acompanhamento

A seção anterior solicitou uma resposta e quaisquer avisos de acompanhamento para **contas e logon**. A resposta inclui as informações do prompt, que estão localizadas em *respostas [0]. contexto*e o texto a ser exibido para o usuário. 

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

A matriz `prompts` fornece texto na propriedade `displayText` e o valor `qnaId`. Você pode mostrar essas respostas como as próximas opções exibidas no fluxo de conversa e, em seguida, enviar o `qnaId` selecionado de volta para QnA Maker na solicitação a seguir. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma solicitação JSON para retornar uma resposta não inicial e prompts de acompanhamento

Preencha o objeto `context` para incluir o contexto anterior.

Na solicitação JSON a seguir, a pergunta atual é *usar o Windows Hello para entrar* e a pergunta anterior era *contas e entrando*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta não inicial e prompts de acompanhamento

A resposta JSON do QnA Maker _GenerateAnswer_ inclui os prompts de acompanhamento na propriedade `context` do primeiro item no objeto `answers`:

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Consultar a base de dados de conhecimento com a ID de QnA Maker

Na resposta da pergunta inicial, qualquer prompt de acompanhamento e seu `qnaId` associado são retornados. Agora que você tem a ID, é possível passá-la no corpo da solicitação do prompt de acompanhamento. Se o corpo da solicitação contiver o `qnaId` e o objeto de contexto (que contém as propriedades anteriores do QnA Maker), GenerateAnswer retornará a pergunta exata por ID, em vez de usar o algoritmo de classificação para localizar a resposta pelo texto da pergunta. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Exibir prompts e enviar o contexto no aplicativo cliente 

Você adicionou prompts na sua base de dados de conhecimento e testou o fluxo no painel de teste. Agora você precisa usar esses prompts no aplicativo cliente. Para o bot Framework, os prompts não são exibidos automaticamente nos aplicativos cliente. Você pode exibir os prompts como ações ou botões sugeridos como parte da resposta à consulta do usuário em aplicativos cliente, incluindo esse [exemplo de estrutura de bot](https://aka.ms/qnamakermultiturnsample) em seu código. O aplicativo cliente deve armazenar a ID de QnA Maker atual e a consulta de usuário e passá-las no [objeto de contexto da API GenerateAnswer](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) para a próxima consulta de usuário. 

## <a name="display-order-is-supported-in-the-update-api"></a>A ordem de exibição tem suporte na API de atualização

O [texto de exibição e a ordem de exibição](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), retornados na resposta JSON, têm suporte para edição pela [API de atualização](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Adicionar ou excluir prompts de várias ativações com a API de atualização

Você pode adicionar ou excluir prompts de várias ativações usando a [API de atualização de QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Os prompts estão adicionando na matriz `promptsToAdd` da propriedade `context` e na matriz `promptsToDelete`. 

## <a name="export-knowledge-base-for-version-control"></a>Exportar base de dados de conhecimento para controle de versão

O QnA Maker [dá suporte ao controle de versão](../concepts/development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) no portal de QnA Maker, incluindo as etapas de conversa de passagem múltipla no arquivo exportado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre conversas contextuais neste [exemplo de caixa de diálogo](https://aka.ms/qnamakermultiturnsample) ou saiba mais sobre [design de bot conceitual para conversas com vários folheios](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
