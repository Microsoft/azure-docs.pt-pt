---
title: 'Tutorial: Criar um bot FAQ com serviço Azure Bot'
description: Neste tutorial, crie um Bot SEM Código FAQ com QnA Maker e Azure Bot Service.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: ab6607175c596a0d82cf75f0ad786a76e85b6959
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612155"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Tutorial: Criar um bot FAQ com serviço Azure Bot
Crie um BOT FAQ com o QnA Maker e o Azure [Bot Service](https://azure.microsoft.com/services/bot-service/) sem código.

Neste tutorial, ficará a saber como:

<!-- green checkmark -->
> [!div class="checklist"]
> * Ligue uma base de conhecimento da QnA Maker a um serviço Azure Bot
> * Implementar o Bot
> * Converse com o Bot no web chat
> * Acenda o Bot nos canais suportados

## <a name="create-and-publish-a-knowledge-base"></a>Criar e publicar uma base de dados de conhecimento

Siga o [quickstart](../Quickstarts/create-publish-knowledge-base.md) para criar uma base de conhecimento. Uma vez publicada a base de conhecimento com sucesso, chegará à página abaixo.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

![Screenshot da publicação bem sucedida](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

![Screenshot da publicação bem sucedida gerida](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint-managed.png)

---

## <a name="create-a-bot"></a>Criar um bot

Após a publicação, pode criar um bot a partir da página **Publicar:**

* Você pode criar vários bots rapidamente, todos apontando para a mesma base de conhecimento para diferentes regiões ou planos de preços para os bots individuais.
* Se quiser apenas um bot para a base de conhecimento, use o Ver todos os seus bots no link **do portal Azure** para ver uma lista dos seus bots atuais.

Quando se faz alterações na base de conhecimento e se republica, não precisa de tomar mais medidas com o bot. Já está configurado para trabalhar com a base de conhecimento, e trabalha com todas as mudanças futuras na base de conhecimento. Sempre que publica uma base de conhecimento, todos os bots ligados a ela são automaticamente atualizados.

1. No portal QnA Maker, na página **Publicar,** selecione **Create bot**. Este botão só aparece depois de publicar a base de conhecimento.

     # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

    ![Screenshot de criar um bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

    ![Screenshot de criar uma pré-visualização gerida por bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page-managed.png)

    ---
    

1. Um novo separador de navegador abre para o portal Azure, com a página de criação do Azure Bot Service. Configure o serviço de bots Azure. O bot e o QnA Maker podem partilhar o plano de serviço de aplicações web, mas não podem partilhar a aplicação web. Isto significa que o nome da **aplicação** para o bot deve ser diferente do nome da aplicação para o serviço QnA Maker.

    * **O que deve fazer**
        * Mude a pega bot - se não for única.
        * Selecione linguagem SDK. Uma vez criado o bot, você pode baixar o código para o seu ambiente de desenvolvimento local e continuar o processo de desenvolvimento.
    * **O que não deve fazer**
        * Altere as seguintes definições no portal Azure ao criar o bot. São pré-povoados para a sua base de conhecimento existente:
           * Chave QnA Auth
           * Plano de serviço de aplicações e localização


1. Após a criação do bot, abra o recurso **de serviço Bot.**
1. Sob **Gestão de** Bot, selecione **Teste no Web Chat**.
1. No pedido de chat do **Tipo da sua mensagem,** insira:

    `Azure services?`

    O chat bot responde com uma resposta da sua base de conhecimento.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Introduza uma consulta do utilizador no chat web de teste.":::
1. Acenda o Bot em [canais adicionais suportados.](/azure/bot-service/bot-service-manage-channels)