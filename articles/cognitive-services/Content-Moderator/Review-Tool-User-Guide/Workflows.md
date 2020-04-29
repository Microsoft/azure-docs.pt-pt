---
title: Defina e utilize fluxos de trabalho de conteúdo através da ferramenta Review - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Pode utilizar o designer de fluxos de trabalho Do Moderador de Conteúdo Azure para definir fluxos de trabalho e limiares personalizados com base nas suas políticas de conteúdo.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72754243"
---
# <a name="define-and-use-moderation-workflows"></a>Definir e utilizar fluxos de trabalho de moderação

Neste guia, você aprenderá a configurar e usar [fluxos de trabalho](../review-api.md#workflows) no site da [ferramenta Review.](https://contentmoderator.cognitive.microsoft.com) Os fluxos de trabalho são filtros personalizados baseados na nuvem que pode usar para lidar com o conteúdo de forma mais eficiente. Os fluxos de trabalho podem ligar-se a uma variedade de serviços para filtrar o conteúdo de diferentes maneiras e, em seguida, tomar as medidas adequadas. Este guia mostra-lhe como utilizar o conector Moderador de Conteúdo (que está incluído por padrão) para filtrar o conteúdo e configurar avaliações humanas num cenário de moderação típica.

## <a name="create-a-new-workflow"></a>Criar um novo fluxo de trabalho

Vá à [ferramenta De Revisão](https://contentmoderator.cognitive.microsoft.com/) de Moderador de Conteúdo e inscreva-se. No separador **Definições,** selecione **Fluxos de Trabalho**.

![Definição de fluxos de trabalho](images/2-workflows-0.png)

No ecrã seguinte, selecione **Adicionar Workflow**.

![Adicione um fluxo de trabalho](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Atribuir um nome e descrição

Nomeie o seu fluxo de trabalho, insira uma descrição e escolha se o fluxo de trabalho irá lidar com imagens ou texto.

![Nome e descrição do fluxo de trabalho](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definir critérios de avaliação

No ecrã seguinte, vá à secção **"Se".** No menu de entrega de cima, escolha **Condição**. Isto permitir-lhe-á configurar a condição em que o fluxo de trabalho irá tomar medidas. Se quiser utilizar várias condições, escolha **a Combinação.** 

Em seguida, selecione um conector. Este exemplo utiliza o Moderador de **Conteúdo**. Dependendo do conector que escolher, obterá diferentes opções para a saída de dados. Consulte a secção de [Conectores](./configure.md#connectors) do guia de definições da ferramenta Rever para aprender a configurar outros conectores.

![Selecione conector de fluxo de trabalho](images/image-workflow-connect-to.PNG)

Escolha a saída desejada para utilizar e defina as condições para a verificar.

![Definir condição de fluxo de trabalho](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definir a ação

Vá à secção **Then,** onde selecione uma ação. O exemplo seguinte cria uma revisão de imagem e atribui uma etiqueta. Opcionalmente, pode adicionar um caminho alternativo (Else) e definir uma ação para isso também.

![Definir ação de fluxo de trabalho](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Salvar o fluxo de trabalho

Note o nome do fluxo de trabalho; precisa do nome para iniciar um trabalho de moderação com a API workflow (ver abaixo). Por fim, guarde o fluxo de trabalho utilizando o botão **Guardar** na parte superior da página.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

Agora que definiu um fluxo de trabalho personalizado, teste-o com conteúdo de amostra. Vá ao **Workflows** e selecione o botão **de execução** de execução correspondente.

![Teste de fluxo de trabalho](images/image-workflow-execute.PNG)

Guarde esta [imagem de amostra](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) para a sua unidade local. Em seguida, **selecione 'Escolher Ficheiros's e** fazer o upload da imagem para o fluxo de trabalho.

![Um corredor com uma citação sobreposta na imagem](images/sample-text.jpg)

### <a name="track-progress"></a>Controlar o progresso

Pode ver o progresso do fluxo de trabalho na próxima janela pop-up.

![Execução de fluxo de trabalho de pista](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Verificar a ação de fluxo de trabalho

Vá ao separador **Image** em **Review** e verifique se existe uma revisão de imagem recém-criada.

![Rever imagens](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a configurar e utilizar fluxos de trabalho de moderação a partir da ferramenta Content Moderator [Review](https://contentmoderator.cognitive.microsoft.com). Em seguida, consulte o [guia REST API](../try-review-api-workflow.md) para aprender a criar fluxos de trabalho programáticamente.
