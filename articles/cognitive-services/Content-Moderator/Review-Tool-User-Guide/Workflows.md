---
title: Definir e utilizar fluxos de trabalho de conteúdo através da ferramenta Review - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Pode utilizar o designer de fluxo de trabalho do Moderador de Conteúdo Azure para definir fluxos de trabalho personalizados e limiares baseados nas suas políticas de conteúdo.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 6eb2a2d2762b60a12bb9a24b92e2edae4b846cd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904147"
---
# <a name="define-and-use-moderation-workflows"></a>Definir e utilizar fluxos de trabalho de moderação

Neste guia, você vai aprender a configurar e usar [fluxos de trabalho](../review-api.md#workflows) no site da [ferramenta Review.](https://contentmoderator.cognitive.microsoft.com) Os fluxos de trabalho são filtros personalizados baseados na nuvem que pode usar para lidar com o conteúdo de forma mais eficiente. Os fluxos de trabalho podem ligar-se a uma variedade de serviços para filtrar o conteúdo de diferentes maneiras e, em seguida, tomar as medidas adequadas. Este guia mostra-lhe como utilizar o conector Content Moderator (que está incluído por padrão) para filtrar o conteúdo e configurar avaliações humanas num cenário de moderação típico.

## <a name="create-a-new-workflow"></a>Criar um novo fluxo de trabalho

Vá à [ferramenta de Análise de Moderador de Conteúdo](https://contentmoderator.cognitive.microsoft.com/) e inscreva-se. No **separador Definições,** selecione **Fluxos de Trabalho**.

![Definição de fluxos de trabalho](images/2-workflows-0.png)

No ecrã seguinte, **selecione Add Workflow**.

![Adicione um fluxo de trabalho](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Atribuir um nome e descrição

Nomeie o seu fluxo de trabalho, introduza uma descrição e escolha se o fluxo de trabalho irá lidar com imagens ou texto.

![Nome e descrição do fluxo de trabalho](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definir critérios de avaliação

No ecrã seguinte, vá para a secção **Se.** No menu de dropdown superior, escolha **Condição**. Isto permitir-lhe-á configurar a condição em que o fluxo de trabalho irá tomar medidas. Se quiser utilizar várias condições, escolha **a Combinação.** 

Em seguida, selecione um conector. Este exemplo utiliza **o Moderador de Conteúdo.** Dependendo do conector que escolher, terá diferentes opções para a saída de dados. Consulte a secção [de Conectores](./configure.md#connectors) do guia de definições da ferramenta 'Rever' para saber como configurar outros conectores.

![Selecione o conector do fluxo de trabalho](images/image-workflow-connect-to.PNG)

Escolha a saída desejada para utilizar e desa estale as condições para a verificar.

![Definir condição de fluxo de trabalho](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definir a ação

Vá à secção **Então,** onde seleciona uma ação. O exemplo a seguir cria uma revisão de imagem e atribui uma etiqueta. Opcionalmente, pode adicionar um caminho alternativo (Else) e definir uma ação para isso também.

![Definir ação de fluxo de trabalho](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Salvar o fluxo de trabalho

Note o nome do fluxo de trabalho; precisa do nome para iniciar um trabalho de moderação com a API do fluxo de trabalho (ver abaixo). Por fim, guarde o fluxo de trabalho utilizando o botão **Guardar** na parte superior da página.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

Agora que definiu um fluxo de trabalho personalizado, teste-o com o conteúdo da amostra. Vá a **Workflows** e selecione o botão **de fluxo de trabalho executar** correspondente.

![Teste de fluxo de trabalho](images/image-workflow-execute.PNG)

Guarde esta [imagem de amostra](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) para a sua unidade local. Em seguida, **selecione Escolha Ficheiro(s)** e carrete a imagem para o fluxo de trabalho.

![Um corredor com uma citação sobreposta na imagem](images/sample-text.jpg)

### <a name="track-progress"></a>Controlar o progresso

Pode ver o progresso do fluxo de trabalho na próxima janela popup.

![Execução de fluxo de trabalho de pista](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Verificar ação de fluxo de trabalho

Vá ao separador **imagem** em **análise** e verifique se existe uma revisão de imagem recentemente criada.

![Rever imagens](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a configurar e utilizar fluxos de trabalho de moderação a partir da ferramenta de [Análise](https://contentmoderator.cognitive.microsoft.com)de Moderadores de Conteúdo. Em seguida, consulte o guia da [consola API](../try-review-api-workflow.md) para aprender a criar fluxos de trabalho programáticamente.
