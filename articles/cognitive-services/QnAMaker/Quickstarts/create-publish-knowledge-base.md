---
title: 'Início rápido: Criar, treinar e publicar a base de dados de conhecimento-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. A base de dados de conhecimento QnA Maker neste exemplo é criada de uma página da Web de perguntas frequentes simples para responder a perguntas sobre a recuperação de chave do BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: aab64822730531acdcf5f3d91ed8bf028ce7cfd4
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971936"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Início rápido: Criar, preparar e publicar a sua base de dados de conhecimento do Criador de FAQ

Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. Este artigo inclui um exemplo de criação de uma base de dados de conhecimento QnA Maker de uma página da Web de perguntas frequentes simples, para responder a perguntas sobre a recuperação de chave do BitLocker.

Inclua uma personalidade Chit para tornar seu conhecimento mais atraente com seus usuários.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Pré-requisito

> [!div class="checklist"]
> * Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-new-qna-maker-knowledge-base"></a>Criar uma nova base de dados de conhecimento QnA Maker

1. Entre no portal do [QnAMaker.ai](https://QnAMaker.ai) com suas credenciais do Azure.

1. No portal de QnA Maker, selecione **criar uma base de dados de conhecimento**.

1. Na página **criar** , selecione **criar um serviço QnA**. Será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar um serviço do Criador de FAQ na sua subscrição. 

1. No portal de QnA Maker, selecione o serviço de QnA Maker nas listas suspensas. Se você criou um novo serviço de QnA Maker, certifique-se de atualizar a página.

   ![Captura de tela da seleção de uma base de dados de conhecimento do serviço QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Nomeie sua base de dados de conhecimento **meu exemplo QnA KB**.

1. Adicione um documento do Word de exemplo como uma URL: 

    `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`

1. Selecione `+ Add URL`.

1. Adicione o  **_Professional_ Chit-Chat** à sua KB. 

1. Selecione **criar a sua BDC**.

    O processo de extração leva alguns minutos para ler o documento e identificar perguntas e respostas.

    Depois que QnA Maker cria com êxito a base de dados de conhecimento, a página **da base de dados de conhecimento** é aberta. Você pode editar o conteúdo da base de dados de conhecimento nesta página.

## <a name="add-a-new-question-and-answer-set"></a>Adicionar uma nova pergunta e um conjunto de respostas

1. No portal de QnA Maker, na página **Editar** , selecione **Adicionar par de QnA**.
1. Adicione a seguinte pergunta: 

    `How many Azure services are used by a knowledge base?`

1. Adicione a resposta formatada com _redução_:

    ` * Azure QnA Maker service\n* Azure Search\n* Azure web app\n* Azure app plan`

    ![ Adicione a pergunta como texto e a resposta formatada com redução.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    O símbolo de redução, `*`, é usado para pontos de marcador. O `\n` é usado para uma nova linha.  

    A página **Editar** mostra a redução. Ao usar o painel de **teste** mais tarde, você verá a redução exibida corretamente. 

## <a name="save-and-train"></a>Guardar e preparar

No canto superior direito, selecione **Guardar e preparar** para guardar as edições e preparar o modelo do Criador de FAQ. As edições só são mantidas se tiverem sido guardadas.

## <a name="test-the-knowledge-base"></a>Testar a base de dados de conhecimento

1. No portal de QnA Maker, no canto superior direito, selecione **testar** para testar se as alterações feitas entraram em vigor. 
1. Insira um exemplo de consulta de usuário na caixa de texto. 

    `How many Azure services are used by a knowledge base?`  

    ![ Insira um exemplo de consulta de usuário na caixa de texto. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Selecione **Inspecionar** para analisar a resposta de forma mais detalhada. A janela de teste é usada para testar suas alterações na base de dados de conhecimento antes de publicar sua base de dados de conhecimento.

1. Selecione **testar** novamente para fechar o painel de **teste** .

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento

Quando você publica uma base de dados de conhecimento, o conteúdo da sua base de dados de conhecimento passa do índice `test` para um índice `prod` no Azure Search.

![Captura de tela da movimentação do conteúdo da sua base de dados de conhecimento](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. No portal de QnA Maker, selecione **publicar**. Depois, para confirmar, selecione **Publicar** na página.

    O serviço Criador de FAQ é agora publicado com êxito. Pode utilizar o ponto final na sua aplicação ou código de bot.

    ![Captura de tela de publicação bem-sucedida](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Criar um bot

Após a publicação, você pode criar um bot na página **publicar** : 

* Você pode criar vários bots rapidamente, todos apontando para a mesma base de dados de conhecimento para diferentes regiões ou planos de preços para os bots individuais. 
* Se você quiser apenas um bot para a base de dados de conhecimento, use o link **Exibir todos os bots no portal do Azure** para exibir uma lista de seus bots atuais. 

Ao fazer alterações na base de dados de conhecimento e republicar, você não precisa realizar mais ação com o bot. Ele já está configurado para trabalhar com a base de dados de conhecimento e funciona com todas as alterações futuras na base de dados de conhecimento. Toda vez que você publica uma base de dados de conhecimento, todos os bots conectados a ela são atualizados automaticamente.

1. No portal de QnA Maker, na página **publicar** , selecione **criar bot**. Esse botão aparece somente depois que você publicou a base de dados de conhecimento.

    ![Captura de tela da criação de um bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Uma nova guia do navegador é aberta para o portal do Azure, com a página de criação do serviço de bot do Azure. Configure o serviço de bot do Azure. 
    
    * Não altere as configurações a seguir no portal do Azure ao criar o bot. Eles são preenchidos previamente para sua base de dados de conhecimento existente: 
        * Chave de autenticação QnA
        * Plano e local do serviço de aplicativo
    * O bot e o QnA Maker podem compartilhar o plano do serviço de aplicativo Web, mas não podem compartilhar o aplicativo Web. Isso significa que o **nome do aplicativo** para o bot deve ser diferente do nome do aplicativo para o serviço de QnA Maker. 

1. Depois que o bot for criado, abra o recurso de **serviço bot** . 
1. Em **Gerenciamento de bot**, selecione **testar no Web Chat**.
1. No prompt de chat de **Digite sua mensagem**, digite:

    `Azure services?`

    O bot de chat responde com uma resposta de sua base de dados de conhecimento. 

    ![Insira uma consulta de usuário no bate-papo da Web de teste.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos do QnA Maker e do bot Framework no portal do Azure. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações:

* [Formato de redução nas respostas](../concepts/data-sources-supported.md)
* [Testando sua redução](../concepts/data-sources-supported.md#testing-your-markdown)
* QnA Maker [fontes de dados](../Concepts/data-sources-supported.md). 
* [Definições de configuração de recurso de bot](../tutorials/create-qna-bot.md).

> [!div class="nextstepaction"]
> [Adicionar perguntas com metadados](add-question-metadata-portal.md)

