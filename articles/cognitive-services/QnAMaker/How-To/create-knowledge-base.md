---
title: 'Início rápido: Criar base de dados de conhecimento-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Use o portal de serviço API de QnA Maker para adicionar criar uma base de dados de conhecimento com o Chit-Chat. Isso faz seu aplicativo se envolver. Adicione um conjunto previamente preenchido do Chit principal – Converse em seu KB como um ponto de partida para o Chit-Chat do bot e economize o tempo e o custo de escrevê-los do zero.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 798bbb725d4764c5ec7a1d69770e9508af0fdf5a
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376218"
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

1. Dê um **nome** ao seu serviço, `my first kb`como. São suportados nomes duplicados e carateres especiais.

1. Adicione a página de solução de problemas QnA Maker como uma `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`URL:, `+ Add URL`em seguida, selecione. Ver mais informações sobre os tipos de origens suportadas [aqui](../Concepts/data-sources-supported.md). Para este guia de início rápido, **não carregue arquivos** para dados que você deseja extrair. Consulte a [obter informações sobre preços](https://aka.ms/qnamaker-pricing) pode adicionar ver o número de documentos.

1. Adicione o  **_Professional_ Chit-Chat** à sua KB. 

1. Selecione **criar a sua BDC**.

    ![Criar KB](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Pode levar alguns minutos para que os dados sejam extraídos.

    ![Extração](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Quando sua base de dados de conhecimento for criada com êxito, você será redirecionado para a página **da base de dados de conhecimento** .

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar a base de dados de conhecimento, remova-a no portal de QnA Maker.

## <a name="next-steps"></a>Passos seguintes

Para medidas de economia de custo, você pode [compartilhar](set-up-qnamaker-service-azure.md#share-existing-services-with-qna-maker) alguns, mas não todos os recursos do Azure criados para QnA Maker.

> [!div class="nextstepaction"]
> [Adicionar perguntas com metadados](../quickstarts/add-question-metadata-portal.md)
