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
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 63eb13dd131fcc1c424c02fdac10f531cc9f0282
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876631"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Suporte de idioma do conteúdo da base de dados de conhecimento para QnA Maker

O QnA Maker dá suporte ao conteúdo da base de dados de conhecimento em muitos idiomas. No entanto, cada serviço de QnA Maker deve ser reservado para uma única linguagem. A primeira base de dados de conhecimento criada visando um determinado serviço de QnA Maker define o idioma desse serviço. Consulte [aqui](../Overview/languages-supported.md) para obter a lista de idiomas com suporte.

O idioma é reconhecido automaticamente do conteúdo das fontes de dados que estão sendo extraídas. Depois de criar um novo serviço de QnA Maker e uma nova base de dados de conhecimento nesse serviço, você pode verificar se o idioma foi definido corretamente.

1. Navegue para o [portal do Azure](https://portal.azure.com/).

1. Selecione **grupos de recursos** e navegue até o grupo de recursos em que o serviço de QnA Maker está implantado e selecione o recurso de **Azure Search** .

    ![Selecionar Azure Search recurso](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Selecione o índice **testkb** . Esse Azure Search índice é sempre o primeiro criado e contém o conteúdo salvo de todas as bases de dados de conhecimento nesse serviço. 

    ![Selecione o KB de teste](../media/qnamaker-how-to-language-kb/select-testkb.png)

1. Selecione a seção **campos** mostrando os detalhes do _testkb_ .

    ![Selecionar Campos](../media/qnamaker-how-to-language-kb/selectfields.png)

1. Marque a caixa do **analisador** para ver os detalhes do idioma.

    ![Selecionar analisador](../media/qnamaker-how-to-language-kb/select-analyzer.png)

1. Você deve descobrir que o _analisador_ está definido como um idioma específico. Esse idioma foi detectado automaticamente durante a etapa de criação da base de dados de conhecimento dos arquivos e URLs importados. Esse idioma não pode ser alterado depois que o recurso é criado.

    ![Analisador selecionado](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um bot QnA com o serviço de bot do Azure](../Tutorials/create-qna-bot.md)
