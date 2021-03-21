---
title: Conversas multi-voltas - QnA Maker
description: Use solicitações e contexto para gerir as múltiplas voltas, conhecidas como multi-voltas, para o seu bot de uma pergunta para outra. Multi-volta é a capacidade de ter uma conversa de trás para a frente onde o contexto da pergunta anterior influencia a próxima pergunta e resposta.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 313d1a390c30e7e5612b8d9bab7783b6698c35fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618496"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Utilizar pedidos de seguimento para criar vários turnos numa conversa

Use instruções de acompanhamento e contexto para gerir as múltiplas voltas, conhecidas como _multi-voltas,_ para o seu bot de uma pergunta para outra.

Para ver como funciona em várias voltas, veja o seguinte vídeo de demonstração:

[![Conversa multi-volta no QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa multi-volta?

Algumas perguntas não podem ser respondidas numa única curva. Quando desenha as conversas da sua aplicação do cliente (chat bot), um utilizador pode fazer uma pergunta que precisa de ser filtrada ou refinada para determinar a resposta correta. Torna possível este fluxo através das perguntas, apresentando ao utilizador *as indicações de seguimento*.

Quando um utilizador faz uma pergunta, o QnA Maker devolve a resposta _e_ quaisquer pedidos de seguimento. Esta resposta permite-lhe apresentar as perguntas de acompanhamento como escolhas.

> [!CAUTION]
> As instruções de várias voltas não são extraídas de documentos faq. Se necessitar de extração multi-volta, remova os pontos de interrogação que designam os pares QnA como PERGUNTAS Frequentes.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exemplo de conversa multi-volta com chat bot

Com multi-volta, um bot de chat gere uma conversa com um utilizador para determinar a resposta final, como mostra a seguinte imagem:

![Um diálogo multi-volta com solicitações que guiam um utilizador através de uma conversa](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, um utilizador iniciou uma conversa ao inserir a **minha conta**. A base de conhecimento tem três pares de perguntas e respostas ligados. Para refinar a resposta, o utilizador seleciona uma das três opções na base de conhecimento. A questão (#1), tem três pedidos de acompanhamento, que são apresentados no chat bot como três opções (#2).

Quando o utilizador seleciona uma opção (#3), é apresentada a próxima lista de opções de refinação (#4). Esta sequência continua (#5) até que o utilizador determine a resposta final correta (#6).

### <a name="use-multi-turn-in-a-bot"></a>Use multi-turn em um bot

Depois de publicar o seu KB, pode selecionar o botão **Criar Bot** para implantar o seu bot QnA Maker para o serviço de bots Azure. As indicações aparecerão nos clientes de chat que ativou para o seu bot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Criar uma conversa multi-volta a partir da estrutura de um documento

Quando cria uma base de conhecimento, a secção **'Povoar' do KB** apresenta uma **extração de várias voltas ativada a partir de URLs, .pdf ou .docx** caixa de verificação de ficheiros.

![Caixa de verificação para permitir a extração em várias voltas](../media/conversational-context/enable-multi-turn.png)

Ao selecionar esta opção, o QnA Maker extrai a hierarquia presente na estrutura do documento. A hierarquia é convertida para dar seguimento às solicitações e a raiz da hierarquia serve como o QnA-mãe. Em alguns documentos, a raiz da hierarquia não tem conteúdo que possa servir de resposta, podendo fornecer o 'Texto de Resposta Padrão' para ser usado como um texto de resposta substituta para extrair tais hierarquias.

A estrutura multi-turn só pode ser deduzida a partir de URLs, ficheiros PDF ou ficheiros DOCX. Para um exemplo de estrutura, veja uma imagem de um [ficheiro PDF manual do utilizador do Microsoft Surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

:::image type="content" source="../media/conversational-context/import-file-with-conversational-structure.png" alt-text="A screenshot mostra um exemplo da estrutura num manual do utilizador." lightbox="../media/conversational-context/import-file-with-conversational-structure.png":::

### <a name="building-your-own-multi-turn-document"></a>Construindo o seu próprio documento multi-volta

Se estiver a criar um documento multi-turn, tenha em mente as seguintes diretrizes:

* Utilize títulos e sub-títulos para denotar hierarquia. Por exemplo, pode h1 denotar o QnA e h2 do progenitor para denotar o QnA que deve ser tomado como rápido. Utilize o tamanho do título pequeno para denotar a hierarquia subsequente. Não utilize estilo, cor ou algum outro mecanismo que insinue estrutura no seu documento, o QnA Maker não extrairá as indicações de várias voltas.

* O primeiro carácter de posição deve ser capitalizado.

* Não termine um título com um ponto de interrogação, `?` .

* Pode utilizar o documento da [amostra](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) como exemplo para criar o seu próprio documento multi-turn.

### <a name="adding-files-to-a-multi-turn-kb"></a>Adicionar ficheiros a um KB multi-turn

Ao adicionar um documento hierárquico, o QnA Maker determina as indicações de seguimento da estrutura para criar fluxo de conversação.

1. No QnA Maker, selecione uma base de conhecimento existente que foi criada com **enable multi-turn extração a partir de URLs, .pdf ou .docx ficheiros.** habilidoso.
1. Aceda à página **Definições,** selecione o ficheiro ou URL para adicionar.
1. **Poupe e treine** a base de conhecimento.

> [!Caution]
> O suporte para a utilização de um ficheiro de base de conhecimento multi-turn de TSV ou XLS exportado como fonte de dados para uma base de conhecimento nova ou vazia não é suportado. É necessário **importar** esse tipo de ficheiro, a partir da página **Definições** do portal QnA Maker, de modo a adicionar solicitações de várias voltas exportadas a uma base de conhecimento.

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Criar base de conhecimento com solicitações de várias voltas com a API Criar

Pode criar um caso de conhecimento com solicitações de várias voltas utilizando a [API do Criador de QnA.](/rest/api/cognitiveservices/qnamaker/knowledgebase/create) As indicações estão adicionando na `context` matriz da `prompts` propriedade.

## <a name="show-questions-and-answers-with-context"></a>Mostrar perguntas e respostas com contexto

Reduza os pares de perguntas e respostas exibidos apenas para aqueles com conversas contextuais.

Selecione **Ver as opções** e, em seguida, selecione **Mostrar o contexto**. A lista apresenta pares de perguntas e respostas que contêm pedidos de seguimento.

![Filtrar pares de perguntas e respostas por conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

O contexto multi-volta é apresentado na primeira coluna.

:::image type="content" source="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png" alt-text="A screenshot mostra a secção de Contexto realçada." lightbox="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png":::

Na imagem anterior, **#1** indica texto arrojado na coluna, o que significa a questão atual. A questão dos pais é o item mais alto da fila. Quaisquer perguntas abaixo estão os pares de perguntas e respostas ligados. Estes itens são selecionáveis, para que possa ir imediatamente para os outros itens de contexto.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicione um par de perguntas e respostas existente como um pedido de seguimento

A pergunta original, **A minha conta,** tem pedidos de acompanhamento, tais como **Contas e assinaturas.**

![As respostas "Contas e assinatura" e as instruções de acompanhamento](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Adicione um pedido de seguimento a um par de perguntas e respostas existente que não esteja atualmente ligado. Como a pergunta não está ligada a nenhum par de perguntas e respostas, a definição de visão atual tem de ser alterada.

1. Para ligar um par de perguntas e respostas existente como um pedido de seguimento, selecione a linha para o par de perguntas e respostas. Para o manual Surface, procure **por Iniciar sê-lo** para reduzir a lista.
1. Na linha para **Sign out**, na coluna **Resposta,** selecione **Adicionar o pedido de acompanhamento**.
1. Nos campos na janela pop-up **de acompanhamento,** introduza os seguintes valores:

    |Campo|Valor|
    |--|--|
    |Texto a apresentar|Introduza **Desligue o dispositivo**. Este texto é personalizado para exibir na mensagem de seguimento.|
    |Apenas contexto| Selecione esta caixa de verificação. Uma resposta só é devolvida se a pergunta especificar o contexto.|
    |Link para responder|Introduza **Utilize o ecrã de entrada** para encontrar o par de perguntas e respostas existente.|

1.  Uma partida é devolvida. Selecione esta resposta como seguimento e, em seguida, **selecione Guardar**.

    ![A página "Pedido de acompanhamento (PREVIEW)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Depois de adicionar o pedido de seguimento, **selecione Save e treine** na navegação superior.

### <a name="edit-the-display-text"></a>Editar o texto do ecrã

Quando um pedido de seguimento é criado e um par de perguntas e respostas existente é introduzido como o **Link to Answer**, pode introduzir novo texto do **Display**. Este texto não substitui a questão existente, e não adiciona uma nova pergunta alternativa. É separado desses valores.

1. Para editar o texto do ecrã, procure e selecione a pergunta no campo **Contexto.**
1. Na linha para essa pergunta, selecione o pedido de seguimento na coluna de resposta.
1. Selecione o texto de exibição que pretende editar e, em seguida, **selecione Editar**.

    ![O comando Editar para o texto do exibição](../media/conversational-context/edit-existing-display-text.png)

1. Na janela **pop-up de seguimento,** altere o texto de visualização existente.
1. Quando terminar de editar o texto do visor, **selecione Guardar**.
1. Na barra de navegação de topo, **salve e treine.**

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicione um novo par de perguntas e respostas como um pedido de acompanhamento

Quando se adiciona um novo par de perguntas e respostas à base de conhecimento, cada par deve estar ligado a uma pergunta existente como um pedido de seguimento.

1. Na barra de ferramentas base de conhecimento, procure e selecione o par de perguntas e respostas existente para **contas e inscreva-se em**.

1. Na coluna **Resposta** para esta pergunta, selecione **Adicionar o pedido de acompanhamento**.
1. No **pedido de acompanhamento (PREVIEW)**, crie um novo pedido de acompanhamento introduzindo os seguintes valores:

    |Campo|Valor|
    |--|--|
    |Texto a apresentar|*Criar uma conta windows*. O texto personalizado para visualizar na mensagem de seguimento.|
    |Apenas contexto|Selecione esta caixa de verificação. Esta resposta só é devolvida se a pergunta especificar o contexto.|
    |Link para responder|Introduza o seguinte texto como resposta:<br>*[Criar](https://account.microsoft.com/) uma conta Windows com uma conta de e-mail nova ou existente.*<br>Quando guardar e treinar a base de dados, este texto será convertido. |
    |||

    ![Criar uma nova pergunta rápida e responder](../media/conversational-context/create-child-prompt-from-parent.png)

1. **Selecione Criar novo** e, em seguida, selecione **Guardar**.

    Esta ação cria um novo par de perguntas e respostas e liga a pergunta selecionada como um pedido de seguimento. A coluna **Contexto,** para ambas as perguntas, indica uma relação rápida de seguimento.

1. Selecione **Ver as opções** e, em seguida, selecione [**Mostrar o contexto (PREVIEW)**](#show-questions-and-answers-with-context).

    A nova pergunta mostra como está ligada.

    ![Criar um novo pedido de acompanhamento](../media/conversational-context/new-qna-follow-up-prompt.png)

    A questão dos pais mostra uma nova questão como uma das suas escolhas.

    :::image type="content" source="../media/conversational-context/child-prompt-created.png" alt-text="A screenshot mostra a coluna Contexto, para ambas as perguntas, indica uma relação rápida de seguimento." lightbox="../media/conversational-context/child-prompt-created.png":::

1. Depois de adicionar o pedido de seguimento, **selecione Save e treine** na barra de navegação superior.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Ver multi-volta durante o teste de solicitações de seguimento

Quando se testa a pergunta com instruções de seguimento no painel de **teste,** a resposta inclui as indicações de seguimento.

![A resposta inclui as solicitações de seguimento](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Um pedido de JSON para devolver uma resposta inicial e pedidos de acompanhamento

Utilize o objeto vazio `context` para solicitar a resposta à pergunta do utilizador e inclua pedidos de seguimento.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para devolver uma resposta inicial e pedidos de seguimento

A secção anterior solicitou uma resposta e quaisquer solicitações de acompanhamento às **Contas e à sua assinatura.** A resposta inclui a informação rápida, que se encontra em *respostas[0].contextual,* e o texto a apresentar ao utilizador.

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
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n  Go to Start, and right-click your name. Then select Sign out. ",
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

A `prompts` matriz fornece texto na propriedade e o `displayText` `qnaId` valor. Pode mostrar estas respostas como as próximas escolhas exibidas no fluxo de conversação e, em seguida, enviar o selecionado `qnaId` de volta para o QnA Maker no seguinte pedido.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Um pedido de JSON para devolver uma resposta não inicial e pedidos de seguimento

Preencha o `context` objeto para incluir o contexto anterior.

No seguinte pedido JSON, a questão atual é utilizar o *Windows Hello para iniciar sessão* e a pergunta anterior foi contas e iniciar *sessão.*

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para devolver uma resposta não inicial e pedidos de seguimento

A resposta do QnA Maker _GenerateAnswer_ JSON inclui as indicações de seguimento na `context` propriedade do primeiro item do `answers` objeto:

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
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Consultar a base de conhecimento com o ID do Fabricante QnA

Se estiver a construir uma aplicação personalizada utilizando uma função multi-turn. Na resposta inicial, quaisquer pedidos de seguimento e seus associados `qnaId` são devolvidos. Agora que tem a identificação, pode passar isso no corpo de pedido do pedido de acompanhamento. Se o corpo de pedido contiver o `qnaId` , e o objeto de contexto (que contém as propriedades anteriores do QnA Maker), então GenerateAnswer devolverá a pergunta exata por ID, em vez de usar o algoritmo de classificação para encontrar a resposta pelo texto de pergunta.

## <a name="display-order-is-supported-in-the-update-api"></a>A ordem de exibição é suportada na API de atualização

O [texto de exibição e a ordem de exibição](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), devolvidos na resposta JSON, são suportados para edição pela [API de atualização](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Adicione ou elimine as solicitações de várias voltas com a API de atualização

Pode adicionar ou eliminar pedidos de várias voltas utilizando a [API de Atualização do Criador de QnA](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  As indicações estão adicionando na `context` matriz da propriedade e na `promptsToAdd` `promptsToDelete` matriz.

## <a name="export-knowledge-base-for-version-control"></a>Base de conhecimento de exportação para controlo de versão

O QnA Maker suporta o controlo da versão, incluindo passos de conversação em várias voltas no ficheiro exportado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre conversas contextuais a partir desta amostra de [diálogo](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj) ou saiba mais sobre [design conceptual de bot para conversas multi-voltas.](/azure/bot-service/bot-builder-conversations)

> [!div class="nextstepaction"]
> [Migrar para uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)