---
title: Criar, preparar, publicar a base de dados de conhecimento – Criador de FAQ
titleSuffix: Azure Cognitive Services
description: Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. A base de dados de conhecimento do QnA Maker neste exemplo é criada a partir de uma página de FAQ Web se simples para responder perguntas sobre recuperação de chaves do BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: cf6b0cd99254d5b034687598c588ce5ef13dbd53
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272857"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Criar, preparar e publicar a sua base de dados de conhecimento do Criador de FAQ

Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. A base de dados de conhecimento do QnA Maker neste exemplo é criada a partir de uma página de FAQ Web se simples para responder perguntas sobre recuperação de chaves do BitLocker.

## <a name="prerequisite"></a>Pré-requisito

> [!div class="checklist"]
> * Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-qna-maker-knowledge-base"></a>Criar uma Base de Dados de Conhecimento do Criador de FAQ

1. Inicie sessão no [QnAMaker.ai](https://QnAMaker.ai) portal com as suas credenciais do Azure.

1. No portal do QnA Maker, selecione **criar uma base de dados de conhecimento**.

   ![Criar nova base de dados de conhecimento](../media/qna-maker-create-kb.png)

1. Na pagina **Criar**, no passo 1, selecione **Criar um serviço do Criador de FAQ**. Será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar um serviço do Criador de FAQ na sua subscrição. Se o portal do Azure exceder o tempo limite, selecione **Tentar novamente** no site. Depois de estabelecer ligação, o dashboard do Azure é apresentado.

1. Depois de criar um novo serviço do Criador de FAQ no Azure com êxito, volte a qnamaker.ai/criar. Selecione o seu serviço de FAQ nas listas pendentes no passo 2. Se tiver criado um novo serviço de FAQ, certifique-se de que atualiza a página.

   ![Selecione uma base de dados de conhecimento de serviço do QnA](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Passo 3, nome de sua base de dados de conhecimento **KB de QnA do meu exemplo**.

1. Para adicionar conteúdo à sua base de dados de conhecimento, selecione os três tipos de origens de dados. No passo 4, em **Preencher a sua BDC**, adicione o URL [FAQ de Recuperação do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) à **caixa**.

   ![Selecione uma base de dados de conhecimento de serviço do QnA](../media/qnamaker-quickstart-kb/add-datasources.png)

1. No passo 5, selecione **Criar a sua BDC**.

1. Enquanto está a ser criada a base de dados de conhecimento, é apresentada uma janela de pop-up. O processo de extração demora alguns minutos a ler a página HTML e a identificar as perguntas e as respostas.

1. Quando a base de dados de conhecimento é criada com êxito, o **base de dados de conhecimento** é aberta a página. Pode editar o conteúdo da base de dados de conhecimento nesta página.

## <a name="edit-the-knowledge-base"></a>Editar a base de dados de conhecimento

1. No portal do QnA Maker, sobre o **editar** secção, **QnA adicionar par** para adicionar uma nova linha para a base de dados de conhecimento. Em **Pergunta**, introduza **Olá.** Em **Resposta**, introduza **Hello. Pergunte-me perguntas do BitLocker.**

    ![Adicione um par de FAQ](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. No canto superior direito, selecione **Guardar e preparar** para guardar as edições e preparar o modelo do Criador de FAQ. As edições só são mantidas se tiverem sido guardadas.

## <a name="test-the-knowledge-base"></a>Testar a base de dados de conhecimento

1. No portal do QnA Maker, no canto superior direito, selecione **testar** para testar a que as alterações que efetuou ocorreu em vigor. ENTER `hi there` na caixa e selecione Enter. Deverá ver a resposta que criou como resposta.

1. Selecione **Inspecionar** para analisar a resposta de forma mais detalhada. A janela de teste é utilizada para testar as suas alterações para a base de dados de conhecimento, antes de publicadas.

    ![Painel de Teste](../media/qnamaker-quickstart-kb/inspect-panel.png)

1. Selecione **Testar** novamente para fechar a pop-up **Testar**.

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento

Quando publica uma base de dados de conhecimento, move o conteúdo de perguntas e respostas da sua base de dados de conhecimento do índice de teste para um índice de produção no Azure search.

![Publicar o índice de teste de produção](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. No portal do QnA Maker, no menu junto a **edite**, selecione **Publish**. Depois, para confirmar, selecione **Publicar** na página.

1. O serviço Criador de FAQ é agora publicado com êxito. Pode utilizar o ponto final na sua aplicação ou código de bot.

    ![Publicar](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Criar um bot

Criação de um bot está sempre disponível após a publicação, do **publicar** página. 

* Pode criar várias bots rapidamente, tudo o que aponta para a mesma base de dados de conhecimento para diferentes regiões ou planos para o bots individuais de preços. 
* Se pretender que apenas um bot para a base de dados de conhecimento, utilize o **ver todos os seus bots no portal do Azure** ligação para ver uma lista dos seus bots atual. 
* Quando efetuar alterações à base de dados de conhecimento e volte a publicar, não é necessário qualquer ação adicional com o bot. Ele já está configurado para trabalhar com a base de dados de conhecimento e funcionará com todas as alterações futuras para a base de dados de conhecimento. Sempre que publicar uma base de dados de conhecimento - todos os bots ligadas à mesma são atualizadas automaticamente

1. No portal do QnA Maker, sobre o **Publish** página, selecione **criar bot**. Esse botão aparece apenas após a publicação de base de dados de conhecimento.

    ![No portal do QnA Maker, aceda à página de publicação e publicar a sua base de dados de conhecimento. Selecione criar Bot.](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. É aberto um novo separador do browser para o portal do Azure, com a página de criação do Azure Bot Service. Configure o serviço de bot do Azure. Para obter mais informações sobre estas definições de configuração, consulte [criar um QnA Bot com o Azure Bot Service v4](../tutorials/create-qna-bot.md) tutorial.
    
    * Não altere as seguintes definições no portal do Azure ao criar o bot. Elas são pré-preenchidos para sua base de dados de conhecimento existente: 
        * Chave de autenticação do QnA
        * Plano do serviço de aplicações/localização
        * Storage do Azure
    * O bot e o QnA Maker podem partilhar o serviço de aplicações web _plano_ , mas não é possível partilhar a aplicação web. Isso significa que o **nome da aplicação** tem de ser diferente do nome da aplicação que utilizou quando criou o serviço QnA Maker. 


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md)
