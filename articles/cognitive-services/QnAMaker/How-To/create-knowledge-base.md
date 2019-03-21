---
title: Criar uma base de dados de conhecimento
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Utilizar o portal de serviço de API do QnA Maker, para adicionar criar uma base de dados de conhecimento com chit-bate-papo. Isso torna seu aplicativo envolvente. Adicionar um conjunto preenchido previamente de chat-chit principal em sua BDC como ponto de partida para chit-bate-papo o bot e economizar o tempo e o custo de escrevê-los a partir do zero.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: b4553a392795bb8578f24848ccacc870b654bce9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116237"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Início rápido: Criar uma base de dados de conhecimento com o portal de serviço de API do QnA Maker

O portal de serviço de API do QnA Maker torna simples para adicionar as suas origens de dados existente durante a criação de uma base de dados de conhecimento. Pode criar uma nova base de dados de conhecimento do QnA Maker partir os seguintes tipos de documento:

<!-- added for scanability -->
* Páginas de perguntas frequentes
* Manuais de produtos
* Documentos estruturados

Inclua uma personalidade chit-bate-papo para tornar o seu conhecimento mais interessantes com os seus utilizadores.

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

1. Adicione **Chit-bate-papo** para sua KB. Opte por adicionar o suporte para o bot, chit chat ao escolher entre uma as 3 personalidades. 

    ![Adicionar chit-bate-papo para KB](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Selecione **criar a sua BDC**.

    ![Criar KB](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Demora alguns minutos para os dados a ser extraído.

    ![Extração](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Quando a sua Base de dados de conhecimento é criado com êxito, será redirecionado para o **base de dados de conhecimento** página.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com a base de dados de conhecimento, removê-lo no portal do QnA Maker.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Adicionar o pessoal de chit-bate-papo](./chit-chat-knowledge-base.md)
