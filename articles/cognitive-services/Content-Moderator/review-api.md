---
title: Opiniões, Fluxos de Trabalho e Conceitos de Emprego - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Neste artigo, você vai aprender sobre os conceitos fundamentais da ferramenta Review; avaliações, fluxos de trabalho e empregos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221152"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Avaliações de moderação de conteúdos, fluxos de trabalho e empregos

O Moderador de Conteúdo combina moderação assistida por máquinas com capacidades humanas no loop para criar um processo de moderação ideal para cenários do mundo real. Fá-lo através da [ferramenta](https://contentmoderator.cognitive.microsoft.com)de revisão baseada em nuvem. Neste guia, você aprenderá sobre os conceitos fundamentais da ferramenta Review: avaliações, fluxos de trabalho e empregos.

## <a name="reviews"></a>Revisões

Numa análise, o conteúdo é enviado para a ferramenta 'Rever' e aparece sob o separador **'Rever'.** A partir daqui, os utilizadores podem alterar as etiquetas aplicadas e aplicar as suas próprias etiquetas personalizadas conforme apropriado. Quando um utilizador submete uma revisão, os resultados são enviados para um ponto final de chamada especificado, e o conteúdo é removido do site.

![Site de ferramentas de revisão aberto em um navegador, no separador Review](./Review-Tool-user-Guide/images/image-workflow-review.png)

Consulte o guia da [ferramenta Review](./review-tool-user-guide/review-moderated-images.md) para começar a criar comentários, ou consulte o guia [REST API](./try-review-api-review.md) para aprender a fazê-lo programáticamente.

## <a name="workflows"></a>Fluxos de trabalho

Um fluxo de trabalho é um filtro personalizado baseado em nuvem para conteúdo. Os fluxos de trabalho podem ligar-se a uma variedade de serviços para filtrar o conteúdo de diferentes maneiras e, em seguida, tomar as medidas adequadas. Com o conector Moderador de Conteúdo, um fluxo de trabalho pode aplicar automaticamente etiquetas de moderação e criar avaliações com conteúdo submetido.

### <a name="view-workflows"></a>Ver fluxos de trabalho

Para visualizar os fluxos de trabalho existentes, vá à [ferramenta 'Rever'](https://contentmoderator.cognitive.microsoft.com/) e selecione **Definições** > **Fluxos de Trabalho**.

![Fluxo de trabalho padrão](images/default-workflow-listed.PNG)

Os fluxos de trabalho podem ser completamente descritos como cordas JSON, o que as torna acessíveis programáticamente. Se selecionar a opção **Editar** para o seu fluxo de trabalho e, em seguida, selecionar o separador **JSON,** verá uma expressão JSON como a seguinte:

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

Consulte o guia da [ferramenta Review](./review-tool-user-guide/workflows.md) para começar a criar e utilizar fluxos de trabalho, ou consulte o guia [REST API](./try-review-api-workflow.md) para aprender a fazê-lo programáticamente.

## <a name="jobs"></a>Tarefas

Um trabalho de moderação serve como uma espécie de invólucro para a funcionalidade de moderação de conteúdos, fluxos de trabalho e revisões. O trabalho digitaliza o seu conteúdo utilizando a API de moderação de imagem moderadora de conteúdo ou a API de moderação de texto e, em seguida, verifica-o contra o fluxo de trabalho designado. Com base nos resultados do fluxo de trabalho, pode ou não criar uma revisão para o conteúdo na [ferramenta Revisão](./review-tool-user-guide/human-in-the-loop.md). Embora tanto as revisões como os fluxos de trabalho possam ser criados e configurados com as respetivas APIs, o trabalho API permite-lhe obter um relatório detalhado de todo o processo (que pode ser enviado para um ponto final de chamada especificado).

Consulte o [guia REST API](./try-review-api-job.md) para começar a usar trabalhos.

## <a name="next-steps"></a>Passos seguintes

* Teste a [consola Job API](try-review-api-job.md)e utilize as amostras de código REST API. Se estiver familiarizado com o C#Visual Studio e , consulte também o [Arranque Rápido Jobs .NET](moderation-jobs-quickstart-dotnet.md). 
* Para comentários, inicie-se com a [consola API review](try-review-api-review.md)e utilize as amostras de código REST API. Em seguida, consulte a secção de comentários do [arranque rápido .NET](dotnet-sdk-quickstart.md).
* Para análises de vídeo, utilize a análise de [vídeo quickstart](video-reviews-quickstart-dotnet.md), e aprenda a [adicionar transcrições à análise de vídeo](video-transcript-reviews-quickstart-dotnet.md).
