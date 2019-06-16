---
title: Definir e utilizar fluxos de trabalho de conteúdo por meio da ferramenta de revisão - Content Moderator
titlesuffix: Azure Cognitive Services
description: Pode utilizar o estruturador de fluxo de trabalho do Azure Content Moderator para definir fluxos de trabalho personalizados e limiares com base em suas diretivas de conteúdo.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61271184"
---
# <a name="define-and-use-moderation-workflows"></a>Definir e utilizar fluxos de trabalho de moderação

Neste guia, aprenderá como configurar e utilizar [fluxos de trabalho](../review-api.md#workflows) sobre o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) Web site. Fluxos de trabalho são filtros personalizados com base na cloud que pode usar para manipular o conteúdo com mais eficiência. Fluxos de trabalho podem ligar a uma variedade de serviços para filtrar o conteúdo de formas diferentes e, em seguida, execute as ações apropriadas. Este guia mostra-lhe como utilizar o conector do Content Moderator (que é incluído por predefinição) para filtrar conteúdo e configurar revisões realizadas por pessoas num cenário típico de moderação.

## <a name="create-a-new-workflow"></a>Criar um novo fluxo de trabalho

Vá para o [ferramenta de revisão de moderador de conteúdo](https://contentmoderator.cognitive.microsoft.com/) e iniciar sessão. Sobre o **configurações** separador, selecione **fluxos de trabalho**.

![Definição de fluxos de trabalho](images/2-workflows-0.png)

No ecrã seguinte, selecione **adicionar fluxos de trabalho**.

![Adicionar um fluxo de trabalho](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Atribuir um nome e descrição

Nomeie o seu fluxo de trabalho, introduza uma descrição e escolha se o fluxo de trabalho irá processar imagens ou texto.

![Nome do fluxo de trabalho e uma descrição](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definir critérios de avaliação

No ecrã seguinte, vá para o **se** secção. No menu pendente superior, escolha **condição**. Isso permitirá que configurar a condição em que o fluxo de trabalho executará uma ação. Se quiser usar várias condições, escolha **combinação** em vez disso. 

Em seguida, selecione um conector. Este exemplo utiliza **Content Moderator**. Consoante o conector que escolher, obterá as diferentes opções para a saída de dados. Consulte a [conectores](./configure.md#connectors) secção do guia de definições de ferramenta de revisão para saber como configurar a outros conectores.

![Selecione o conector de fluxo de trabalho](images/image-workflow-connect-to.PNG)

Escolha a saída desejada para utilizar e definir as condições para verificar em relação.

![Definir a condição de fluxo de trabalho](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definir a ação

Vá para o **, em seguida,** seção, onde poderá selecionar uma ação. O exemplo seguinte cria uma revisão de imagem e atribui uma etiqueta. Opcionalmente, pode adicionar um caminho alternativo de (Else) e defina uma ação para que também.

![Definir ação de fluxo de trabalho](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Guardar o fluxo de trabalho

Tome nota do nome de fluxo de trabalho; é necessário o nome para iniciar uma tarefa de moderação com a API de fluxo de trabalho (ver abaixo). Por último, guarde o fluxo de trabalho utilizando o **guardar** botão na parte superior da página.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

Agora que tenha definido um fluxo de trabalho personalizado, testá-la com conteúdo de exemplo. Aceda a **fluxos de trabalho** e selecione o correspondente **executar o fluxo de trabalho** botão.

![Teste de fluxo de trabalho](images/image-workflow-execute.PNG)

Salvar isso [imagem de exemplo](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) sua unidade local. Em seguida, selecione **Escolher ficheiro ou ficheiros** e carregar a imagem para o fluxo de trabalho.

![Um executor com uma citação sobreposta a imagem](images/sample-text.jpg)

### <a name="track-progress"></a>Controlar o progresso

Pode ver o progresso do fluxo de trabalho na próxima janela de pop-up.

![Controlar a execução de fluxo de trabalho](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Certifique-se a ação de fluxo de trabalho

Vá para o **imagem** separador sob **rever** e certifique-se de que existe uma revisão de imagem recentemente criada.

![Rever imagens](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu a configurar e utilizar fluxos de trabalho de moderação do Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com). Em seguida, veja a [guia de REST API](../try-review-api-workflow.md) para saber como criar fluxos de trabalho por meio de programação.
