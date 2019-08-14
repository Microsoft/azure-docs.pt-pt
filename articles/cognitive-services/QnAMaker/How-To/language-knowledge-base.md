---
title: Base de dados de conhecimento não em inglês-QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker dá suporte ao conteúdo da base de dados de conhecimento em muitos idiomas. No entanto, cada serviço de QnA Maker deve ser reservado para uma única linguagem. A primeira base de dados de conhecimento criada visando um determinado serviço de QnA Maker define o idioma desse serviço.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 26792246267ced6d9fff50fe4fea11cc8d280d6a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966676"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Suporte de idioma do conteúdo da base de dados de conhecimento para QnA Maker
O QnA Maker dá suporte ao conteúdo da base de dados de conhecimento em muitos idiomas. No entanto, cada serviço de QnA Maker deve ser reservado para uma única linguagem. A primeira base de dados de conhecimento criada visando um determinado serviço de QnA Maker define o idioma desse serviço. Consulte [aqui](../Overview/languages-supported.md) para obter a lista de idiomas com suporte.

O idioma é reconhecido automaticamente do conteúdo das fontes de dados que estão sendo extraídas. Depois de criar um novo serviço de QnA Maker e uma nova base de dados de conhecimento nesse serviço, você pode verificar se o idioma foi definido corretamente.

1. Navegue até o [portal do Azure](https://portal.azure.com/).

2. Selecione **grupos de recursos** e navegue até o grupo de recursos em que o serviço de QnA Maker está implantado e selecione o recurso de **Azure Search** .

    ![Selecionar Azure Search recurso](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Selecione o índice **testkb** . Esse Azure Search índice é sempre o primeiro criado e contém o conteúdo salvo de todas as bases de dados de conhecimento nesse serviço. 

    ![Selecione o KB de teste](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Selecione a seção **campos** mostrando os detalhes do testkb.

    ![Selecionar Campos](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Marque a caixa do **analisador** para ver os detalhes do idioma.

    ![Selecionar analisador](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Você deve descobrir que o analisador está definido para um idioma específico. Esse idioma foi detectado automaticamente durante a etapa de criação da base de dados de conhecimento. Esse idioma não pode ser alterado depois que o recurso é criado.

    ![Analisador selecionado](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar um bot QnA com o serviço de bot do Azure](../Tutorials/create-qna-bot.md)
