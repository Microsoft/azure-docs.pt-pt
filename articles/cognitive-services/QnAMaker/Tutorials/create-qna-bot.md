---
title: 'Tutorial: Bot QnA - Azure Bot Service - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Crie um bot de chat QnA da página de publicação para uma base de dados de conhecimento existente. Este bot usa o SDK do bot Framework v4. Você não precisa escrever nenhum código para criar o bot, todo o código é fornecido para você.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ea6e0d266c181d930f3d18171b09d222e53da7ab
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390897"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Tutorial: Criar um bot QnA com o serviço de bot do Azure v4

Crie um bot de chat QnA da página de **publicação** para uma base de dados de conhecimento existente. Este bot usa o SDK do bot Framework v4. Você não precisa escrever nenhum código para criar o bot, todo o código é fornecido para você.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um serviço de bot do Azure a partir de uma base de dados de conhecimento existente
> * Conversar com o bot para verificar que o código está a funcionar 

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma base de dados de conhecimento publicada para este tutorial. Se você não tiver uma, siga as etapas no tutorial [criar e responder do KB](create-publish-query-in-portal.md) para criar uma base de dados de conhecimento QnA Maker com perguntas e respostas.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Criar um QnA Bot

Crie um bot como um aplicativo cliente para a base de dados de conhecimento. 

1. No portal QnA Maker, vá para a página **publicar** e publique sua base de dados de conhecimento. Selecione **criar bot**. 

    ![No portal QnA Maker, vá para a página publicar e publique sua base de dados de conhecimento. Selecione criar bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    O portal do Azure é aberto com a configuração de criação de bot.

1.  Insira as configurações para criar o bot:

    |Definição|Value|Objetivo|
    |--|--|--|
    |Nome do bot|`my-tutorial-kb-bot`|Este é o nome do recurso do Azure para o bot.|
    |Subscription|Consulte finalidade.|Selecione a mesma assinatura que você usou para criar os recursos de QnA Maker.|
    |Resource group|`my-tutorial-rg`|O grupo de recursos usado para todos os recursos do Azure relacionados ao bot.|
    |Location|`west us`|O local do recurso do Azure do bot.|
    |Escalão de preço|`F0`|A camada gratuita para o serviço de bot do Azure.|
    |Nome da aplicação|`my-tutorial-kb-bot-app`|Este é um aplicativo Web para dar suporte apenas ao bot. Esse não deve ser o mesmo nome de aplicativo que seu serviço de QnA Maker já está usando. Não há suporte para o compartilhamento do aplicativo Web de QnA Maker com qualquer outro recurso.|
    |Idioma do SDK|C#|Essa é a linguagem de programação subjacente usada pelo SDK do bot Framework. Suas opções são [C#](https://github.com/Microsoft/botbuilder-dotnet) ou [node. js](https://github.com/Microsoft/botbuilder-js).|
    |Chave de autenticação QnA|**Não alterar**|Esse valor é preenchido para você.|
    |Local/plano do serviço de aplicativo|**Não alterar**|Para este tutorial, o local não é importante.|
    |Storage do Azure|**Não alterar**|Os dados de conversa são armazenados nas tabelas do armazenamento do Azure.|
    |Application Insights|**Não alterar**|O registro em log é enviado para Application Insights.|
    |ID do aplicativo da Microsoft|**Não alterar**|O usuário e a senha do Active Directory são necessários.|

    ![Crie o bot da base de dados de conhecimento com essas configurações.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Aguarde alguns minutos até que a notificação do processo de criação de bot relate o sucesso.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Conversar com o Bot

1. No portal do Azure, abra o novo recurso bot da notificação. 

    ![No portal do Azure, abra o novo recurso bot da notificação.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Em **Gerenciamento de bot**, selecione **testar no Web Chat** e digite `How large can my KB be?`:. O bot responderá com: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Teste o novo bot da base de dados de conhecimento.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Para obter mais informações sobre os bots do Azure, consulte [usar QnA Maker para responder a perguntas](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o bot neste tutorial, remova o bot no portal do Azure. 

Se você criou um novo grupo de recursos para os recursos do bot, exclua o grupo de recursos. 

Se você não criou um novo grupo de recursos, precisará localizar os recursos associados ao bot. A maneira mais fácil é Pesquisar pelo nome do aplicativo bot e bot. Os recursos de bot incluem:

* O plano do serviço de aplicações
* O serviço de pesquisa
* O serviço cognitivo
* O serviço de aplicações
* Opcionalmente, também pode incluir o serviço de informações da aplicação e o armazenamento de dados do application insights


## <a name="related-to-qna-maker-bots"></a>Relacionado a bots de QnA Maker

* A QnA Maker bot de ajuda, usada no portal do QnA Maker, está disponível como um [exemplo de bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support).
    ![QnA Maker ícone do bot de ajuda é um robô vermelho](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* Os [bots de saúde](https://docs.microsoft.com/HealthBot/qna_model_howto) usam QnA Maker como um de seus modelos de [idioma](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conceito: base de dados de conhecimento](../concepts/knowledge-base.md)

## <a name="see-also"></a>Consulte também

- [Gerir a sua base de dados de conhecimento](https://qnamaker.ai)
- [Ativar o seu bot em diferentes canais](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
