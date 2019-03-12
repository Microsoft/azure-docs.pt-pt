---
title: Bot QnA - Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
description: Este tutorial orienta-o através da criação de um bot QnA com o Azure Bot service v3 no portal do Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 61bec775fc8be963ee6fd0768a297420e6eb7ab2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761587"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>Tutorial: Crie um QnA Bot com o Azure Bot Service v3

Este tutorial explica como criar um bot QnA com o Azure Bot service v3 no [portal do Azure](https://portal.azure.com) sem escrever nenhum código. Ligar uma base de dados de conhecimento (KB) publicada para um bot é tão simples como alterar as definições da aplicação bot. 

> [!Note] 
> Este tópico destina-se a versão 3 do SDK do Bot. Pode encontrar a versão 4 [aqui](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs). 

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um serviço de Bot do Azure com o modelo do QnA Maker
> * Conversar com o bot para verificar que o código está a funcionar 
> * Ligar a sua BDC publicado para o bot
> * Testar o bot com uma pergunta

Neste artigo, pode utilizar o QnA Maker livre [serviço](../how-to/set-up-qnamaker-service-azure.md).

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma base de dados de conhecimento publicada para este tutorial. Se não tiver uma, siga os passos em [criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md) para criar um serviço QnA Maker com perguntas e respostas.

## <a name="create-a-qna-bot"></a>Criar um QnA Bot

1. No portal do Azure, selecione **Criar um recurso**.

    ![criação do serviço de bot](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Na caixa de pesquisa, procure **Web App Bot**.

    ![seleção de serviço de bot](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. Em **Bot Service** (Serviço de Bot), indique as informações necessárias:

    - Definir **nome da aplicação** ao nome do seu bot. O nome é utilizado como o subdomínio ao seu bot é implementado na cloud (por exemplo, mynotesbot.azurewebsites.net).
    - Selecione a subscrição, o grupo de recursos, o plano do serviço de aplicações e a localização.

4. Para utilizar os modelos de v3, selecione a versão do SDK da **SDK v3** e o idioma do SDK do **c#** ou **node. js**.

    ![definições do sdk de bot](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Selecione o **perguntas e respostas** modelo para o campo de modelo do Bot, em seguida, guarde as definições de modelo ao selecionar **selecione**.

    ![Guardar bot seleção de modelo de serviço](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Reveja as definições, em seguida, selecione **criar**. Isto cria e implementa o serviço de bot com para o Azure.

    ![Criar bot](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Confirme que o serviço de bot foi implementado.

    - Selecione **notificações** (o ícone de sino que esteja localizado ao longo da margem superior do portal do Azure). A notificação será alterado de **implementação iniciada** ao **implementação concluída com êxito**.
    - Depois da notificação muda para **implementação concluída com êxito**, selecione **Ir para recurso** em que a notificação.

## <a name="chat-with-the-bot"></a>Conversar com o Bot

Selecionando **Ir para recurso** leva-o para o recurso do bot.

Selecione **teste na Web Chat** para abrir o painel de Web Chat. Escreva "Olá" Web Chat.

![QnA bot web bate-papo](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

O bot responde com ". Defina QnAKnowledgebaseId e QnASubscriptionKey nas definições da aplicação. Esta resposta confirma que o seu QnA Bot recebeu a mensagem, mas não existe nenhuma base de dados de conhecimento do QnA Maker associado a ele ainda. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Ligar a sua base de dados de conhecimento do QnA Maker para o bot

1. Open **as configurações do aplicativo** e editar a **QnAKnowledgebaseId**, **QnAAuthKey**e a **QnAEndpointHostName** campos para conter os valores da sua base de dados de conhecimento do QnA Maker.

    ![Definições da aplicação](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. Obtenha o seu ID de base de dados de conhecimento, url de anfitrião e a chave de ponto final do separador Definições da sua base de dados de conhecimento no portal do QnA Maker.

    - Inicie sessão no [do QnA Maker](https://qnamaker.ai)
    - Aceda à sua base de dados de conhecimento
    - Selecione o **definições** separador
    - **Publicar** sua base de dados de conhecimento, se não tiver feito

    ![Valores do QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

## <a name="test-the-bot"></a>Testar o bot

No portal do Azure, selecione **teste na Web Chat** para testar o bot. 

![Bot QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

O QnA Bot responde a partir da sua base de dados de conhecimento.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o bot neste tutorial, remova o bot no portal do Azure. Os serviços de bot incluem:

* O plano do serviço de aplicações
* O serviço de pesquisa
* O serviço cognitivo
* O serviço de aplicações
* Opcionalmente, também pode incluir o serviço de informações da aplicação e o armazenamento de dados do application insights

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Conceito: base de dados de conhecimento](../concepts/knowledge-base.md)

## <a name="see-also"></a>Consulte também

- [Gerir a sua base de dados de conhecimento](https://qnamaker.ai)
- [Ativar o seu bot em diferentes canais](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
