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
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: cafc1e2f3f195301a6c0f9485ebaa10111b08c7d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803056"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Início rápido: Criar, preparar e publicar a sua base de dados de conhecimento do Criador de FAQ

Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. Este artigo inclui um exemplo de criação de uma base de dados de conhecimento QnA Maker de uma página da Web de perguntas frequentes simples, para responder a perguntas sobre a recuperação de chave do BitLocker.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Pré-requisito

> [!div class="checklist"]
> * Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-qna-maker-knowledge-base"></a>Criar uma Base de Dados de Conhecimento do Criador de FAQ

1. Entre no portal do [QnAMaker.ai](https://QnAMaker.ai) com suas credenciais do Azure.

1. No portal de QnA Maker, selecione **criar uma base de dados de conhecimento**.

   ![Captura de tela do portal de QnA Maker](../media/qna-maker-create-kb.png)

1. Na pagina **Criar**, no passo 1, selecione **Criar um serviço do Criador de FAQ**. Será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar um serviço do Criador de FAQ na sua subscrição. Se o portal do Azure exceder o tempo limite, selecione **Tentar novamente** no site. Depois de estabelecer ligação, o dashboard do Azure é apresentado.

1. Depois de criar um novo serviço do Criador de FAQ no Azure com êxito, volte a qnamaker.ai/criar. Selecione seu serviço de QnA Maker nas listas suspensas na etapa 2. Se você criou um novo serviço de QnA Maker, certifique-se de atualizar a página.

   ![Captura de tela da seleção de uma base de dados de conhecimento do serviço QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Na etapa 3, nomeie sua base de dados de conhecimento **meu exemplo QnA KB**.

1. Para adicionar conteúdo à sua base de conhecimento, selecione três tipos de fontes de dados. No passo 4, em **Preencher a sua BDC**, adicione o URL [FAQ de Recuperação do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) à **caixa**.

   ![Captura de tela da adição de fontes de dados](../media/qnamaker-quickstart-kb/add-datasources.png)

1. No passo 5, selecione **Criar a sua BDC**.

1. Enquanto QnA Maker cria a base de dados de conhecimento, uma janela pop-up é exibida. O processo de extração demora alguns minutos a ler a página HTML e a identificar as perguntas e as respostas.

1. Depois que QnA Maker cria com êxito a base de dados de conhecimento, a página **da base de dados de conhecimento** é aberta. Você pode editar o conteúdo da base de dados de conhecimento nesta página.

## <a name="edit-the-knowledge-base"></a>Editar a base de dados de conhecimento

1. No portal de QnA Maker, na seção **Editar** , selecione **Adicionar par de QnA** para adicionar uma nova linha à base de dados de conhecimento. Em **Pergunta**, introduza **Olá.** Em **Resposta**, introduza **Hello. Pergunte-me perguntas sobre o BitLocker.**

    ![Captura de tela do portal de QnA Maker](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. No canto superior direito, selecione **Guardar e preparar** para guardar as edições e preparar o modelo do Criador de FAQ. As edições só são mantidas se tiverem sido guardadas.

## <a name="test-the-knowledge-base"></a>Testar a base de dados de conhecimento

1. No portal de QnA Maker, no canto superior direito, selecione **testar** para testar se as alterações feitas entraram em vigor. Digite `hi there` na caixa e selecione Enter. Deverá ver a resposta que criou como resposta.

1. Selecione **Inspecionar** para analisar a resposta de forma mais detalhada. A janela de teste é usada para testar suas alterações na base de dados de conhecimento antes que elas sejam publicadas.

    ![Captura de tela do painel de teste](../media/qnamaker-quickstart-kb/inspect.png)

1. Selecione **Testar** novamente para fechar a pop-up **Testar**.

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento

Quando você publica uma base de dados de conhecimento, o conteúdo de perguntas e respostas de sua base de dados de conhecimento se move do índice de teste para um índice de produção no Azure Search.

![Captura de tela da movimentação do conteúdo da sua base de dados de conhecimento](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. No portal de QnA Maker, no menu ao lado de **Editar**, selecione **publicar**. Depois, para confirmar, selecione **Publicar** na página.

1. O serviço Criador de FAQ é agora publicado com êxito. Pode utilizar o ponto final na sua aplicação ou código de bot.

    ![Captura de tela de publicação bem-sucedida](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Criar um bot

Após a publicação, você pode criar um bot na página **publicar** : 

* Você pode criar vários bots rapidamente, todos apontando para a mesma base de dados de conhecimento para diferentes regiões ou planos de preços para os bots individuais. 
* Se você quiser apenas um bot para a base de dados de conhecimento, use o link **Exibir todos os bots no portal do Azure** para exibir uma lista de seus bots atuais. 

Ao fazer alterações na base de dados de conhecimento e republicar, você não precisa realizar mais ação com o bot. Ele já está configurado para trabalhar com a base de dados de conhecimento e funciona com todas as alterações futuras na base de dados de conhecimento. Toda vez que você publica uma base de dados de conhecimento, todos os bots conectados a ela são atualizados automaticamente.

1. No portal de QnA Maker, na página **publicar** , selecione **criar bot**. Esse botão aparece somente depois que você publicou a base de dados de conhecimento.

    ![Captura de tela da criação de um bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Uma nova guia do navegador é aberta para o portal do Azure, com a página de criação do serviço de bot do Azure. Configure o serviço de bot do Azure. Para obter mais informações sobre essas definições de configuração, consulte [criar um bot QnA com o serviço de bot do Azure v4](../tutorials/create-qna-bot.md).
    
    * Não altere as configurações a seguir no portal do Azure ao criar o bot. Eles são preenchidos previamente para sua base de dados de conhecimento existente: 
        * Chave de autenticação QnA
        * Plano e local do serviço de aplicativo
        * Storage do Azure
    * O bot e o QnA Maker podem compartilhar o plano do serviço de aplicativo Web, mas não podem compartilhar o aplicativo Web. Isso significa que o **nome do aplicativo** deve ser diferente do nome do aplicativo usado quando você criou o serviço de QnA Maker. 


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md)
