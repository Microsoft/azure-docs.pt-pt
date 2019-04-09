---
title: Bot QnA - Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
description: Crie um chatbot de FAQ da página de publicação para uma base de dados de conhecimento existente. Este bot usa o SDK do Bot Framework v4. Não é necessário escrever qualquer código para criar o bot, todo o código é fornecido por si.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.openlocfilehash: 85b0004288a06a834b61f6e3d50017d35d66ce86
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263881"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Tutorial: Crie um QnA Bot com o Azure Bot Service v4

Crie um chatbot de FAQ do **publicar** página para uma base de dados de conhecimento existente. Este bot usa o SDK do Bot Framework v4. Não é necessário escrever qualquer código para criar o bot, todo o código é fornecido por si.

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um serviço de Bot do Azure a partir de uma base de dados de conhecimento existente
> * Conversar com o bot para verificar que o código está a funcionar 

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma base de dados de conhecimento publicada para este tutorial. Se não tiver uma, siga os passos em [criar e a resposta da KB](create-publish-query-in-portal.md) tutorial para criar uma base de dados de conhecimento do QnA Maker com perguntas e respostas.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Criar um QnA Bot

Crie um bot como uma aplicação de cliente para a base de dados de conhecimento. 

1. No portal do QnA Maker, vá para o **publicar** de páginas e publicar a sua base de dados de conhecimento. Selecione **criar Bot**. 

    ![No portal do QnA Maker, aceda à página de publicação e publicar a sua base de dados de conhecimento. Selecione criar Bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    É aberto o portal do Azure com a configuração de criação do bot.

1.  Introduza as definições para criar o bot:

    |Definição|Valor|Objetivo|
    |--|--|--|
    |Nome do bot|`my-tutorial-kb-bot`|Este é o nome de recurso do Azure para o bot.|
    |Subscrição|Ver objetivo.|Selecione a mesma subscrição que utilizou para criar os recursos do QnA Maker.|
    |Grupo de recursos|`my-tutorial-rg`|O grupo de recursos utilizado para todos os relacionados com o bot recursos do Azure.|
    |Localização|`west us`|Localização de recursos do Azure o bot.|
    |Escalão de preço|`F0`|O escalão gratuito para o Azure bot service.|
    |Nome da aplicação|`my-tutorial-kb-bot-app`|Esta é uma aplicação web para suportar o seu bot apenas. Não deve ser o mesmo nome como o serviço QnA Maker já está a utilizar. Não é suportada a partilha a aplicação de web do QnA Maker com qualquer outro recurso.|
    |Idioma SDK|C#|Esta é a linguagem de programação subjacente utilizada pelo bot framework SDK. As opções são C# ou node. js.|
    |Chave de autenticação do QnA|**Não altere**|Este valor é preenchido por si.|
    |Plano do serviço de aplicações/localização|**Não altere**|Para este tutorial, a localização não é importante.|
    |Armazenamento do Azure|**Não altere**|Dados de conversação são armazenados em tabelas de armazenamento do Azure.|
    |Application Insights|**Não altere**|O registo é enviado para o Application Insights.|
    |ID de aplicação da Microsoft|**Não altere**|Utilizador do Active Directory e a palavra-passe é necessária.|

    ![Crie o bot de base de dados de conhecimento com estas definições.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Aguarde alguns minutos até que a notificação de processo de criação do bot comunica êxito.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Conversar com o Bot

1. No portal do Azure, abra o novo recurso de bot de notificação. 

    ![No portal do Azure, abra o novo recurso de bot de notificação.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Partir **gestão de Bot**, selecione **teste na Web Chat** e introduza: `How large can my KB be?`. O bot irá responder com: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Teste o novo bot de base de dados de conhecimento.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Para obter mais informações sobre os Bots do Azure, consulte [criador de FAQ de utilização para responder a perguntas](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>Relacionados aos bots de QnA Maker

* O bot de ajuda do QnA Maker, utilizado no portal do QnA Maker, está disponível como um [exemplo de bot](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![Ícone de bot de ajuda do QnA Maker é robô vermelho](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Cuidados de saúde bots](https://docs.microsoft.com/HealthBot/qna_model_howto) utilize o QnA Maker como um dos respetivos [modelos de linguagem](https://docs.microsoft.com/HealthBot/qna_model_howto).

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o bot neste tutorial, remova o bot no portal do Azure. 

Se tiver criado um novo grupo de recursos para os recursos do bot, elimine o grupo de recursos. 

Se não tiver criado um novo grupo de recursos, terá de encontrar os recursos associados com o bot. A maneira mais fácil é procurar por nome da aplicação bot e bot. Os recursos de bot incluem:

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
