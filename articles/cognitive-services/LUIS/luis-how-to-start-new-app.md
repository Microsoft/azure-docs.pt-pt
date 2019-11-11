---
title: Criar um novo aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: Crie e gerencie seus aplicativos na página da Web Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904291"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar um novo aplicativo LUIS no portal do LUIS
Há algumas maneiras de criar um aplicativo LUIS. Você pode criar um aplicativo LUIS no portal do LUIS ou por meio das [APIs](developer-reference-resource.md)de criação do Luis.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Usando o portal do LUIS

Você pode criar um novo aplicativo no portal de visualização de várias maneiras:

* Comece com um aplicativo vazio e crie intenções, declarações e entidades.
* Comece com um aplicativo vazio e adicione um [domínio predefinido](luis-how-to-use-prebuilt-domains.md).
* Importe um aplicativo LUIS de um arquivo `.lu` ou `.json` que já contenha intenções, declarações e entidades.

## <a name="using-the-authoring-apis"></a>Usando as APIs de criação
Você pode criar um novo aplicativo com as APIs de criação de duas maneiras:

* [Adicionar aplicativo](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) -inicie com um aplicativo vazio e crie intenções, declarações e entidades.
* [Adicionar aplicativo precompilado](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) -inicie com um domínio predefinido, incluindo intenções, declarações e entidades.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Criar novo aplicativo no LUIS

1. Na página **meus aplicativos** , selecione sua assinatura e crie o recurso e, em seguida, **+ criar**. Se você estiver usando a chave de avaliação gratuita, saiba como [criar um recurso de criação](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

    ![Lista de aplicações LUIS](./media/create-app-in-portal.png)


1. Na caixa de diálogo, digite o nome do seu aplicativo, como `Pizza Tutorial`.

    ![Caixa de diálogo Criar novo aplicativo](./media/create-pizza-tutorial-app-in-portal.png)

1. Escolha a cultura do aplicativo e, em seguida, selecione **concluído**. A descrição e o recurso de previsão são opcionais neste ponto. Você pode definir, a qualquer momento, na seção **gerenciar** do Portal.

    > [!NOTE]
    > A cultura não pode ser alterada assim que a aplicação for criada. 

    Depois que o aplicativo for criado, o portal do LUIS mostrará a lista de **tentativas** com a intenção de `None` já criada para você. Agora você tem um aplicativo vazio. 
    
    > [!div class="mx-imgBorder"]
    > ![lista de tentativas com nenhuma tentativa criada sem nenhum exemplo de declarações.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Outras ações disponíveis

A barra de ferramentas de contexto fornece outras ações:

* Renomear aplicativo
* Importar do arquivo usando `.lu` ou `.json`
* Exportar aplicativo como `.lu` (para [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json`ou `.zip` (para [contêiner Luis](luis-container-howto.md))
* Importar logs de ponto de extremidade de contêiner para examinar o ponto de extremidade declarações
* Exportar logs de ponto de extremidade, como um `.csv`, para análise offline
* Excluir aplicativo

## <a name="next-steps"></a>Passos seguintes

Se o design do aplicativo incluir a detecção de intenção, [crie novas tentativas](luis-how-to-add-intents.md)e adicione o exemplo declarações. Se o design do aplicativo for apenas extração de dados, adicione o exemplo declarações à intenção nenhum, [crie entidades](luis-how-to-add-example-utterances.md)e Rotule o exemplo declarações com essas entidades. 
