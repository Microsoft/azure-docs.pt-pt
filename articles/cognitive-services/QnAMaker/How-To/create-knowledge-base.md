---
title: Criar base de dados de conhecimento-QnA Maker
titleSuffix: Azure Cognitive Services
description: Use o portal de serviço API de QnA Maker para adicionar criar uma base de dados de conhecimento com o Chit-Chat. Isso faz seu aplicativo se envolver. Adicione um conjunto previamente preenchido do Chit principal – Converse em seu KB como um ponto de partida para o Chit-Chat do bot e economize o tempo e o custo de escrevê-los do zero.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dfd0663c09b78539a1d6b78aa7820385b9ddbdca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563079"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Início rápido: Criar uma base de dados de conhecimento usando o portal do serviço API de QnA Maker

O portal do serviço API de QnA Maker simplifica a adição de suas fontes de dados existentes ao criar uma base de conhecimento. Pode criar uma nova base de dados de conhecimento do QnA Maker partir os seguintes tipos de documento:

<!-- added for scanability -->
* Páginas de perguntas frequentes
* Manuais de produtos
* Documentos estruturados

Inclua uma personalidade Chit para tornar seu conhecimento mais atraente com seus usuários.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="create-a-new-knowledge-base"></a>Criar uma nova base de dados de conhecimento

1. Iniciar sessão para o [portal do QnA Maker](https://qnamaker.ai) com as suas credenciais do Azure e selecione **criar uma base de dados de conhecimento**.

1. Se ainda não tenha criado um serviço QnA Maker, selecione **criar um serviço QnA**. 

1. Selecione o seu inquilino do Azure, o nome da subscrição do Azure e o nome de recurso do Azure associados com o serviço QnA Maker listas no **passo 2** no portal do QnA Maker. Selecione o serviço QnA Maker do Azure que irá alojar a Base de dados de conhecimento.

    ![Configurar o serviço QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Introduza o nome da sua base de dados de conhecimento e as origens de dados para a nova base de dados de conhecimento.

    ![Origens de dados do conjunto](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Dê o seu serviço um **nome.** São suportados nomes duplicados e carateres especiais.
    - Adicione URLs de dados que pretende extraídos. Ver mais informações sobre os tipos de origens suportadas [aqui](../Concepts/data-sources-supported.md).
    - Carregar ficheiros para dados que pretende extraídos. Consulte a [obter informações sobre preços](https://aka.ms/qnamaker-pricing) pode adicionar ver o número de documentos.
    - Se pretender adicionar manualmente QnAs, pode avançar **passo 4** mostrado na imagem anterior.

1. Adicione **Chit-bate-papo** para sua KB. Escolha entre adicionar o suporte ao Chit para o bot, escolhendo um dos personalidades. 

    ![Adicionar Chit-Chat a KB](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Selecione **criar a sua BDC**.

    ![Criar KB](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Demora alguns minutos para os dados a ser extraído.

    ![Extração](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Quando a sua Base de dados de conhecimento é criado com êxito, será redirecionado para o **base de dados de conhecimento** página.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar a base de dados de conhecimento, remova-a no portal de QnA Maker.

## <a name="next-steps"></a>Passos seguintes

Para medidas de economia de custo, você pode [compartilhar](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) alguns, mas não todos os recursos do Azure criados para QnA Maker.

> [!div class="nextstepaction"]
> [Adicionar o pessoal de chit-bate-papo](./chit-chat-knowledge-base.md)
