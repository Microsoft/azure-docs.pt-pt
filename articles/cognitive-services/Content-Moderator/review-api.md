---
title: Conceitos de análises, fluxos de trabalho e trabalhos – Content Moderator
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre análises, fluxos de trabalho e trabalhos
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 0050e2b687b6001514d1ae80c269b1a0499efbea
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757299"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Análises, fluxos de trabalho e trabalhos de moderação de conteúdo

O Content Moderator combina moderação assistida por computador com recursos humanos no loop para criar um processo de moderação ideal para cenários do mundo real. Ele faz isso por meio da ferramenta de [revisão](https://contentmoderator.cognitive.microsoft.com)baseada em nuvem. Neste guia, você aprenderá sobre os principais conceitos da ferramenta de revisão: análises, fluxos de trabalho e trabalhos.

## <a name="reviews"></a>Revisões

Em uma revisão, o conteúdo é carregado para a ferramenta de revisão e aparece na guia **revisão** . A partir daqui, os usuários podem alterar as marcas aplicadas e aplicar suas próprias marcas personalizadas, conforme apropriado. Quando um usuário envia uma revisão, os resultados são enviados para um ponto de extremidade de retorno de chamada especificado e o conteúdo é removido do site.

![Examinar o site da ferramenta aberto em um navegador, na guia revisão](./Review-Tool-user-Guide/images/image-workflow-review.png)

Consulte o [Guia de ferramentas de revisão](./review-tool-user-guide/review-moderated-images.md) para começar a criar revisões ou consulte o [guia da API REST](./try-review-api-review.md) para saber como fazer isso programaticamente.

## <a name="workflows"></a>Fluxos de trabalho

Um fluxo de trabalho é um filtro personalizado baseado em nuvem para conteúdo. Os fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar conteúdo de maneiras diferentes e, em seguida, executar a ação apropriada. Com o conector de Content Moderator, um fluxo de trabalho pode aplicar automaticamente marcas de moderação e criar revisões com conteúdo enviado.

### <a name="view-workflows"></a>Exibir fluxos de trabalho

Para exibir os fluxos de trabalho existentes, vá para a [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) e selecione **configurações**  >  fluxos de**trabalho**.

![Fluxo de trabalho padrão](images/default-workflow-listed.PNG)

Os fluxos de trabalho podem ser completamente descritos como cadeias de caracteres JSON, o que os torna acessíveis programaticamente. Se você selecionar a opção **Editar** para seu fluxo de trabalho e, em seguida, selecionar a guia **JSON** , verá uma expressão JSON semelhante à seguinte:

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

Consulte o [Guia de ferramentas de revisão](./review-tool-user-guide/workflows.md) para começar a criar e usar fluxos de trabalho ou consulte o [guia da API REST](./try-review-api-workflow.md) para saber como fazer isso programaticamente.

## <a name="jobs"></a>Tarefas

Um trabalho de moderação serve como um tipo de wrapper para a funcionalidade de moderação de conteúdo, fluxos de trabalho e revisões. O trabalho examina seu conteúdo usando a API de moderação de imagem Content Moderator ou a API de moderação de texto e, em seguida, verifica-o no fluxo de trabalho designado. Com base nos resultados do fluxo de trabalho, pode ou não criar uma revisão para o conteúdo da [ferramenta de revisão](./review-tool-user-guide/human-in-the-loop.md). Embora as revisões e os fluxos de trabalho possam ser criados e configurados com suas respectivas APIs, a API de trabalhos permite que você obtenha um relatório detalhado de todo o processo (que pode ser enviado para um ponto de extremidade de retorno de chamada especificado).

Consulte o [guia da API REST](./try-review-api-job.md) para começar a usar os trabalhos.

## <a name="next-steps"></a>Passos seguintes

* Teste o [console da API de trabalho](try-review-api-job.md)e use os exemplos de código da API REST. Se você estiver familiarizado com o Visual Studio C#e também Confira o [início rápido dos trabalhos .net](moderation-jobs-quickstart-dotnet.md). 
* Para revisões, comece com o [console de API de revisão](try-review-api-review.md)e use os exemplos de código da API REST. Em seguida, consulte o [início rápido do .net de revisões](moderation-reviews-quickstart-dotnet.md).
* Para revisões de vídeo, use o guia de [início rápido de revisão de vídeo](video-reviews-quickstart-dotnet.md)e saiba como [Adicionar transcrições à revisão de vídeo](video-transcript-reviews-quickstart-dotnet.md).
