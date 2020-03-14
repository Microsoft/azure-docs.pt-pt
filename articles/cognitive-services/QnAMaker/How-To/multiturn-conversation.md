---
title: Conversas multi-turn - QnA Maker
description: Utilize instruções e contexto para gerir as múltiplas voltas, conhecidas como multi-turn, para o seu bot de uma pergunta para outra. Multi-turn é a capacidade de ter uma conversa de trás para a frente onde o contexto da pergunta anterior influencia a próxima pergunta e resposta.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: abdde09fbb1f6b066772366c5cea933824cb5864
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220624"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Utilizar pedidos de seguimento para criar vários turnos numa conversa

Utilize instruções de seguimento e contexto para gerir as múltiplas voltas, conhecidas como _multi-turn,_ para o seu bot de uma pergunta para outra.

Para ver como funciona a multi-volta, veja o seguinte vídeo de demonstração:

[![conversa multi-turn no QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa multi-turn?

Algumas perguntas não podem ser respondidas de uma só vez. Ao conceber as conversas da sua aplicação de cliente (chat bot), um utilizador pode fazer uma pergunta que precisa de ser filtrada ou refinada para determinar a resposta correta. Faz com que isto flua através das questões possíveis apresentando ao utilizador *instruções de acompanhamento*.

Quando um utilizador faz uma pergunta, o QnA Maker devolve a resposta _e_ quaisquer solicitações de seguimento. Esta resposta permite-lhe apresentar as perguntas de acompanhamento como escolhas.

> [!CAUTION]
> As solicitações de várias voltas não são extraídas de documentos de FAQ. Se necessitar de extração em várias voltas, remova os pontos de interrogação que designam os pares QnA como PERGUNTAS.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exemplo de conversa multi-turn com chat bot

Com várias voltas, um chat bot gere uma conversa com um utilizador para determinar a resposta final, como mostra a seguinte imagem:

![Um diálogo multi-turn com solicitações que guiam um utilizador através de uma conversa](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, um utilizador iniciou uma conversa ao entrar na **minha conta.** A base de conhecimento tem três pares de perguntas e respostas ligados. Para refinar a resposta, o utilizador seleciona uma das três opções na base de conhecimentos. A questão (#1), tem três instruções de acompanhamento, que são apresentadas no chat bot como três opções (#2).

Quando o utilizador seleciona uma opção (#3), é apresentada a próxima lista de opções de refinação (#4). Esta sequência continua (#5) até que o utilizador determine a resposta final correta (#6).


### <a name="use-multi-turn-in-a-bot"></a>Use multi-turn em um bot

Depois de publicar o seu KB, pode selecionar o bot **Create Bot** para implantar o seu bot QnA Maker para o serviço de bots Azure. As solicitações aparecerão nos clientes de chat que você ativou para o seu bot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Criar uma conversa multi-turn a partir da estrutura de um documento

Quando cria uma base de conhecimento, a secção Povoada da **sua secção KB** apresenta uma **extração de várias voltas de URLs, .pdf ou .docx ficheiros** check box.

![Verifique a caixa para permitir a extração de várias voltas](../media/conversational-context/enable-multi-turn.png)

Ao selecionar esta opção, o Fabricante QnA extrai a hierarquia presente na estrutura do documento. A hierarquia é convertida para seguir os pedidos e a raiz da hierarquia serve como o qnA-mãe. Em alguns documentos, a raiz da hierarquia não tem conteúdo que possa servir de resposta, pode fornecer o 'Texto de Resposta Padrão' a ser usado como um texto de resposta substituto para extrair tais hierarquias.

A estrutura de várias voltas só pode ser inferida a partir de URLs, ficheiros PDF ou ficheiros DOCX. Para um exemplo de estrutura, veja uma imagem de um ficheiro PDF manual do [utilizador do Microsoft Surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

![! [Exemplo de estrutura num manual do utilizador] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Construindo o seu próprio documento multi-turn

Se estiver a criar um documento multi-turn, tenha em mente as seguintes orientações:

* Utilize rubricas e sub-rubricas para denotar a hierarquia. Por exemplo, pode h1 para denotar o QnA e h2 dos pais para denotar o QnA que deve ser tomado como solicitado. Utilize o pequeno tamanho da cabeça para denotar a hierarquia subsequente. Não utilize o estilo, a cor ou algum outro mecanismo para implicar estrutura no seu documento, o Fabricante QnA não extrairá os avisos de várias voltas.

* O primeiro carácter da direção deve ser capitalizado.

* Não termine um título com um ponto de interrogação, `?`.

* Pode utilizar o documento da [amostra](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) como exemplo para criar o seu próprio documento multi-turn.

### <a name="adding-files-to-a-multi-turn-kb"></a>Adicionar ficheiros a um KB multi-turn

Ao adicionar um documento hierárquico, o QnA Maker determina as solicitações de seguimento da estrutura para criar fluxo de conversação.

1. No QnA Maker, selecione uma base de conhecimento existente que foi criada com **a extração de multi-turn de UrLs, ficheiros .pdf ou .docx.** habilitado.
1. Vá à página **Definições,** selecione o ficheiro ou URL para adicionar.
1. **Salve e treine** a base de conhecimento.

> [!Caution]
> O suporte para a utilização de um ficheiro base de base de conhecimento multi-turn TSV ou XLS exportado como fonte de dados para uma nova base de conhecimento vazia ou vazia não é suportado. É necessário **importar** esse tipo de ficheiro, a partir da página **Definições** do portal QnA Maker, de modo a adicionar solicitações multi-turn exportadas para uma base de conhecimento.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Criar base de conhecimento com solicitações multi-turn com a Create API

Pode criar um caso de conhecimento com solicitações multi-turn utilizando o [Fabricante QnA Create API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). As instruções estão adicionando na matriz de `prompts` da propriedade `context`.

## <a name="show-questions-and-answers-with-context"></a>Mostrar perguntas e respostas com contexto

Reduza os pares de perguntas e respostas exibidos apenas àqueles com conversas contextuais.

Selecione **as opções de visualização,** e, em seguida, selecione **o contexto do show**. A lista apresenta pares de perguntas e respostas que contêm instruções de seguimento.

![Filtrar pares de perguntas e respostas por conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

O contexto multi-turn é apresentado na primeira coluna.

![! [A coluna "Context (PREVIEW)"] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Na imagem anterior, **#1** indica texto arrojado na coluna, o que significa a questão atual. A questão dos pais é o item de topo da fila. Quaisquer perguntas abaixo são os pares de perguntas e respostas ligados. Estes itens são selecionáveis, para que possa ir imediatamente para os outros itens de contexto.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicione um par de perguntas e respostas existente como um pedido de seguimento

A pergunta original, **A minha conta,** tem solicitações de acompanhamento, como **Contas e sessão.**

![As respostas "Contas e sessão" e instruções de acompanhamento](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Adicione um pedido de seguimento a um par de perguntas e respostas existente que não está atualmente ligado. Como a questão não está ligada a nenhum par de perguntas e respostas, a configuração atual da vista tem de ser alterada.

1. Para ligar um par de perguntas e respostas existente como um pedido de seguimento, selecione a linha para o par de perguntas e respostas. Para o manual Surface, procure **o Sign out** para reduzir a lista.
1. Na linha para **Signout**, na coluna **Resposta,** selecione **Adicionar pedido de seguimento**.
1. Nos campos da janela **pop-up de seguimento,** introduza os seguintes valores:

    |Campo|Valor|
    |--|--|
    |Texto de exibição|Introduza **desligar o dispositivo**. Este é um texto personalizado para exibir no pedido de seguimento.|
    |Apenas para contexto| Selecione esta caixa de verificação. Uma resposta só é devolvida se a pergunta especificar o contexto.|
    |Link para responder|Introduza **Utilize o ecrã de entrada** para encontrar o par de perguntas e respostas existente.|


1.  Uma partida é devolvida. Selecione esta resposta como seguimento e, em seguida, selecione **Guardar**.

    ![A página "Follow-up prompt (PREVIEW)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Depois de adicionar o pedido de seguimento, selecione **Guardar e treinar** na navegação superior.

### <a name="edit-the-display-text"></a>Editar o texto do ecrã

Quando for criada uma solicitação de seguimento e um par de perguntas e respostas existente é introduzido como o **Link to Answer,** pode introduzir um novo **texto de exibição**. Este texto não substitui a questão existente, e não adiciona uma nova pergunta alternativa. É separado desses valores.

1. Para editar o texto do ecrã, procure e selecione a questão no campo **Contexto.**
1. Na fila para essa pergunta, selecione o pedido de seguimento na coluna de resposta.
1. Selecione o texto de exibição que pretende editar e, em seguida, **selecione Editar**.

    ![O comando Editar para o texto de exibição](../media/conversational-context/edit-existing-display-text.png)

1. Na janela **pop-up de seguimento,** altere o texto de exibição existente.
1. Quando terminar de editar o texto do visor, selecione **Guardar**.
1. Na barra de navegação superior, **poupe e treine.**


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicione um novo par de perguntas e respostas como um pedido de seguimento

Quando você adicionar um novo par de perguntas e respostas à base de conhecimento, cada par deve estar ligado a uma pergunta existente como um pedido de seguimento.

1. Na barra de ferramentas base de conhecimento, procure e selecione o par de perguntas e respostas existente para **Contas e iniciar sessão em**.

1. Na coluna **Resposta** para esta pergunta, selecione **Adicionar pedido de seguimento**.
1. Sob **solicitação de seguimento (PREVIEW)** , crie um novo pedido de acompanhamento, entrando nos seguintes valores:

    |Campo|Valor|
    |--|--|
    |Texto de exibição|*Criar uma conta Windows*. O texto personalizado para exibir no pedido de seguimento.|
    |Apenas para contexto|Selecione esta caixa de verificação. Esta resposta só é devolvida se a pergunta especificar o contexto.|
    |Link para responder|Introduza o seguinte texto como resposta:<br>*[Criar](https://account.microsoft.com/) uma conta Windows com uma conta de e-mail nova ou existente.*<br>Quando guardar e treinar a base de dados, este texto será convertido. |
    |||

    ![Criar uma nova pergunta e resposta prontas](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selecione **Criar novo**, e, em seguida, selecione **Guardar**.

    Esta ação cria um novo par de perguntas e respostas e liga a pergunta selecionada como um pedido de seguimento. A coluna **Context,** para ambas as perguntas, indica uma relação rápida de seguimento.

1. Selecione **as opções de visualização**, e, em seguida, selecione [**o contexto do programa (PREVIEW)** ](#show-questions-and-answers-with-context).

    A nova pergunta mostra como está ligada.

    ![Criar um novo pedido de acompanhamento](../media/conversational-context/new-qna-follow-up-prompt.png)

    A pergunta dos pais mostra uma nova pergunta como uma das suas escolhas.

    ![! [A coluna Context, para ambas as perguntas, indica uma relação rápida de seguimento] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Depois de adicionar o pedido de seguimento, selecione **Guardar e treine** na barra de navegação superior.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Ativar várias voltas durante os testes de instruções de seguimento

Quando testar a pergunta com instruções de seguimento no painel de **teste,** **selecione Ativar várias voltas**e, em seguida, introduza a sua pergunta. A resposta inclui as solicitações de seguimento.

![A resposta inclui as solicitações de seguimento](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Se não permitir a volta múltipla, a resposta é devolvida, mas as instruções de seguimento não são devolvidas.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Um pedido da JSON para devolver uma resposta inicial e instruções de acompanhamento

Utilize o objeto `context` vazio para solicitar a resposta à pergunta do utilizador e inclua solicitações de seguimento.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma resposta da JSON para devolver uma resposta inicial e instruções de acompanhamento

A secção anterior solicitou uma resposta e quaisquer instruções de acompanhamento para **contas e sessão.** A resposta inclui a informação rápida, que está localizada em *respostas[0].context,* e o texto para exibir ao utilizador.

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

A matriz `prompts` fornece texto na propriedade `displayText` e no valor `qnaId`. Pode mostrar estas respostas como as próximas escolhas exibidas no fluxo de conversação e, em seguida, enviar o `qnaId` selecionado de volta para O Criador qnA no seguinte pedido.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Um pedido da JSON para devolver uma resposta não inicial e instruções de acompanhamento

Preencha o `context` objeto para incluir o contexto anterior.

No seguinte pedido da JSON, a questão atual é *use o Windows Hello para iniciar sessão* e a pergunta anterior foi contas e *sessão.*

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma resposta json para devolver uma resposta não inicial e instruções de acompanhamento

A resposta QnA Maker _GenerateAnswer_ JSON inclui as solicitações de seguimento na propriedade `context` do primeiro item no objeto `answers`:

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Consulta da base de conhecimento com o ID do Fabricante qnA

Se estiver a construir uma aplicação personalizada utilizando recurso multi-turn. Na resposta inicial da pergunta, quaisquer solicitações de acompanhamento e a sua `qnaId` associada são devolvidas. Agora que tem a identificação, pode passar isto no corpo de pedido do pedido de acompanhamento. Se o organismo de pedido contiver o `qnaId`, e o objeto de contexto (que contém as propriedades anteriores do QnA Maker), então a GenerateAnswer devolverá a pergunta exata por ID, em vez de usar o algoritmo de classificação para encontrar a resposta pelo texto de pergunta.


## <a name="display-order-is-supported-in-the-update-api"></a>A ordem de exibição é suportada na API de atualização

O texto de exibição e a ordem de [exibição,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)devolvidos na resposta JSON, são suportados para edição pela API de [atualização](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Adicione ou elimine as solicitações de várias voltas com a API de atualização

Pode adicionar ou eliminar instruções de várias voltas utilizando o [API de atualização do fabricante qnA](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  As solicitações estão adicionando na matriz de `promptsToAdd` da propriedade `context` e na matriz de `promptsToDelete`.

## <a name="export-knowledge-base-for-version-control"></a>Base de conhecimentos de exportação para controlo de versão

O QnA Maker suporta o controlo da versão, incluindo passos de conversação multi-turn no ficheiro exportado.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre conversas contextuais a partir desta amostra de [diálogo](https://aka.ms/qnamakermultiturnsample) ou saiba mais sobre [design de bot conceptual para conversas multi-turn](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrar uma base de conhecimento](../Tutorials/migrate-knowledge-base.md)
