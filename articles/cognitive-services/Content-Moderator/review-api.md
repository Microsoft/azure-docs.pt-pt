---
title: As revisões, fluxos de trabalho e conceitos de tarefas - Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre as revisões de fluxos de trabalho e tarefas
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607275"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Revisões de moderação de conteúdos, os fluxos de trabalho e tarefas

O Content Moderator combina moderação assistida por computador com recursos humanos em loop para criar um processo de moderação ideal para cenários do mundo real. Ele faz isso por meio de baseado na nuvem [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com). Neste guia, aprenderá sobre os conceitos principais da ferramenta de revisão: revisões, fluxos de trabalho e tarefas.

## <a name="reviews"></a>Revisões

Numa revisão, o conteúdo é carregado para a ferramenta de revisão e aparece sob o **rever** separador. Aqui, os utilizadores podem alterar as etiquetas aplicadas e aplicar a suas próprias etiquetas personalizadas conforme apropriado. Quando um usuário envia uma revisão, os resultados são enviados para um ponto de extremidade do retorno de chamada especificado e o conteúdo é removido do site.

![Abra o Web site de ferramenta de revisão num browser, na guia revisão](./Review-Tool-user-Guide/images/image-workflow-review.png)

Consulte a [guia de ferramenta de revisão](./review-tool-user-guide/review-moderated-images.md) para começar a criar análises, ou consulte a [guia de REST API](./try-review-api-review.md) para saber como fazê-lo por meio de programação.

## <a name="workflows"></a>Fluxos de trabalho

Um fluxo de trabalho é um filtro personalizado com base na cloud para o conteúdo. Fluxos de trabalho podem ligar a uma variedade de serviços para filtrar o conteúdo de formas diferentes e, em seguida, execute as ações apropriadas. Com o conector do Content Moderator, um fluxo de trabalho automaticamente pode aplicar etiquetas de moderação e crie revisões com conteúdo submetido.

### <a name="view-workflows"></a>Ver fluxos de trabalho

Para ver os seus fluxos de trabalho existentes, vá para o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) e selecione **definições** > **fluxos de trabalho**.

![Fluxo de trabalho predefinida](images/default-workflow-listed.PNG)

Fluxos de trabalho podem ser completamente descritos como cadeias de caracteres do JSON, que as torna acessível por meio de programação. Se selecionar a **edite** opção para seu fluxo de trabalho e, em seguida, selecione a **JSON** guia, verá uma expressão de JSON semelhante ao seguinte:

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

Consulte a [guia de ferramenta de revisão](./review-tool-user-guide/workflows.md) para começar a criar e utilizar fluxos de trabalho, ou consulte a [guia de REST API](./try-review-api-workflow.md) para saber como fazê-lo por meio de programação.

## <a name="jobs"></a>Tarefas

Uma tarefa de moderação serve como uma espécie de invólucro para a funcionalidade de moderação de conteúdos, os fluxos de trabalho e revisões. A tarefa analisa o conteúdo usando a moderação de imagens de Content Moderator, API ou a API de moderação de texto e, em seguida, verifica-lo contra o fluxo de trabalho designado. Com base nos resultados de fluxo de trabalho, pode ou não pode criar uma revisão para o conteúdo do [ferramenta de revisão](./review-tool-user-guide/human-in-the-loop.md). Embora as revisões e fluxos de trabalho podem ser criados e configurados com seus respectivos APIs, a tarefa de API permite-lhe obter um relatório detalhado de todo o processo (que pode ser enviado para um ponto de extremidade do retorno de chamada especificado).

Consulte a [guia de REST API](./try-review-api-job.md) para começar a utilizar as tarefas.

## <a name="next-steps"></a>Passos Seguintes

* Faça o test drive da [consola de API de tarefa](try-review-api-job.md)e utilize os exemplos de código da REST API. Se estiver familiarizado com o Visual Studio e c#, também, veja a [guia de introdução do .NET de tarefas](moderation-jobs-quickstart-dotnet.md). 
* Para revisões, comece com o [consola de API de revisão](try-review-api-review.md)e utilize os exemplos de código da REST API. Em seguida, consulte a [guia de introdução do .NET de revisões](moderation-reviews-quickstart-dotnet.md).
* Para revisões de vídeo, utilize o [guia de introdução do vídeo revisão](video-reviews-quickstart-dotnet.md)e saiba como [adicionar transcrições para a revisão de vídeo](video-transcript-reviews-quickstart-dotnet.md).
