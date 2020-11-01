---
title: Comentários, fluxos de trabalho e conceitos de emprego - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Neste artigo, você vai aprender sobre os conceitos fundamentais da ferramenta Review; revisões, fluxos de trabalho e empregos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: d98f2390be3b3f4b3770125185cb33daa5ff6371
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93143644"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Avaliações de moderação de conteúdos, fluxos de trabalho e empregos

O Content Moderator combina moderação assistida por máquinas com capacidades humanas no loop para criar um processo de moderação ideal para cenários do mundo real. Fá-lo através da [ferramenta review](https://contentmoderator.cognitive.microsoft.com)baseada na nuvem. Neste guia, você vai aprender sobre os conceitos fundamentais da ferramenta Review: avaliações, fluxos de trabalho e empregos.

## <a name="reviews"></a>Revisões

Numa análise, o conteúdo é enviado para a ferramenta 'Avaliação'. Pode vê-lo clicando no seu tipo de conteúdo no **separador 'Rever'** no painel de instrumentos. A partir do ecrã de revisão, pode alterar as etiquetas aplicadas e aplicar as suas próprias etiquetas personalizadas conforme apropriado. Quando submete uma revisão, os resultados são enviados para um ponto final de retorno especificado, e o conteúdo é removido do site.

> [!div class="mx-imgBorder"]
> ![Rever site de ferramentas com separador de revisão selecionado](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>Gerir comentários

A partir do painel de instrumentos, navegue até **Admin**  ->  **Manage Reviews** para ver o ecrã de administração. Aqui, pode ver uma lista de todas as avaliações (pendentes e concluídas).

O botão **Ações** de três pontos em cada revisão permite-lhe ir ao ecrã de revisão ou inspecionar o histórico dessa revisão.

> [!div class="mx-imgBorder"]
> ![Rever site de ferramentas, no ecrã de Revisão](./Review-Tool-user-Guide/images/manage-reviews.png)

Utilize a barra de ferramentas **Search** para classificar as avaliações por uma variedade de categorias, tais como estado de revisão, tags, tipo de conteúdo, subtemas, utilizadores designados e data criada/modificada.

> [!div class="mx-imgBorder"]
> ![Rever site de ferramentas com separador de revisão selecionado](./Review-Tool-user-Guide/images/review-search.png)

Consulte o guia de [ferramentas Review](./review-tool-user-guide/review-moderated-images.md) para começar a criar avaliações ou consulte o [guia REST API](./try-review-api-review.md) para aprender a fazê-lo programáticamente.

## <a name="workflows"></a>Fluxos de trabalho

Um fluxo de trabalho é um filtro personalizado à base de nuvem para conteúdo. Os fluxos de trabalho podem ligar-se a uma variedade de serviços para filtrar o conteúdo de diferentes maneiras e, em seguida, tomar as medidas adequadas. Com o conector Do Moderador de Conteúdo, um fluxo de trabalho pode aplicar automaticamente etiquetas de moderação e criar revisões com conteúdo submetido.

### <a name="view-workflows"></a>Ver fluxos de trabalho

Para ver os fluxos de trabalho existentes, vá à [ferramenta 'Rever'](https://contentmoderator.cognitive.microsoft.com/) e selecione **Admin**  >  **fluxos de trabalho de administração.**

> [!div class="mx-imgBorder"]
> ![Fluxo de trabalho predefinido](images/default-workflow-list.png)

Os fluxos de trabalho são definidos como cordas JSON, o que os torna acessíveis programáticamente. Se selecionar a opção **Editar** para o seu fluxo de trabalho e, em seguida, selecionar o separador **JSON,** verá uma expressão JSON como as seguintes:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Consulte o guia da [ferramenta Review](./review-tool-user-guide/workflows.md) para começar a criar e utilizar fluxos de trabalho, ou consulte o guia [da API REST](./try-review-api-workflow.md) para aprender a fazê-lo programáticamente.

## <a name="jobs"></a>Tarefas

Um trabalho de moderação serve como uma espécie de invólucro para a funcionalidade de moderação de conteúdos, fluxos de trabalho e avaliações. O trabalho digitaliza o seu conteúdo utilizando a API de moderação de imagem do Moderador de Conteúdo ou a API de moderação de texto e, em seguida, verifica-o contra o fluxo de trabalho designado. Com base nos resultados do fluxo de trabalho, pode ou não criar uma revisão para o conteúdo na [ferramenta Review](./review-tool-user-guide/human-in-the-loop.md). Embora tanto as revisões como os fluxos de trabalho possam ser criados e configurados com as respetivas APIs, a API de trabalho permite-lhe obter um relatório detalhado de todo o processo (que pode ser enviado para um ponto final de retorno específico).

Consulte o [guia da API REST](./try-review-api-job.md) para começar a usar empregos.

## <a name="next-steps"></a>Passos seguintes

* Teste a [consola Job API](try-review-api-job.md)e utilize as amostras de código REST API. Se você está familiarizado com Visual Studio e C#, também confira o [quickstart Jobs .NET](moderation-jobs-quickstart-dotnet.md). 
* Para comentários, inicie com a [consola API de revisão](try-review-api-review.md)e use as amostras de código REST API. Em seguida, consulte a secção de comentários do [arranque rápido .NET](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
* Para análises de vídeo, use o [quickstart](video-reviews-quickstart-dotnet.md)da revisão de vídeo e aprenda a [adicionar transcrições à revisão de vídeo](video-transcript-reviews-quickstart-dotnet.md).