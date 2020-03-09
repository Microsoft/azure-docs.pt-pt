---
title: Criar uma nova app - LUIS
titleSuffix: Azure Cognitive Services
description: Criar e gerir as suas aplicações na página de Web de compreensão de idiomas (LUIS).
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390133"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar uma nova app LUIS no portal LUIS
Existem duas formas para criar uma aplicação do LUIS. Pode criar uma aplicação LUIS no portal LUIS, ou através da AUTORA DA [LUIS.](developer-reference-resource.md)

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Com o portal de LUIS

Pode criar uma nova aplicação no portal de pré-visualização de várias formas:

* Começar com uma aplicação vazia e crie objetivos, expressões e entidades.
* Comece com uma aplicação vazia e adicione um [domínio pré-construído.](luis-how-to-use-prebuilt-domains.md)
* Importar uma aplicação LUIS de um ficheiro `.lu` ou `.json` que já contenha intenções, declarações e entidades.

## <a name="using-the-authoring-apis"></a>Com as APIs de criação
Pode criar uma nova aplicação com as APIs de criação de duas formas:

* [Adicione a aplicação](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - comece com uma app vazia e crie intençãos, expressões e entidades.
* [Adicione aplicação pré-construída](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - comece com um domínio pré-construído, incluindo intenções, expressões e entidades.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Criar nova aplicação LUIS

1. Na página **Minhas Apps,** selecione a sua subscrição e autor de recursos **então + Criar**. Se estiver a usar a chave de teste gratuita, aprenda a criar um recurso de [autoria.](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)

    ![Lista de aplicações LUIS](./media/create-app-in-portal.png)


1. Na caixa de diálogo, introduza o nome da sua aplicação, como `Pizza Tutorial`.

    ![Criar nova caixa de diálogo da aplicação](./media/create-pizza-tutorial-app-in-portal.png)

1. Escolha a sua cultura de aplicação e, em seguida, selecione **Done**. A descrição e o recurso de previsão são opcionais neste momento. Pode definir então a qualquer momento na secção **Gerir** do portal.

    > [!NOTE]
    > A cultura não pode ser alterada assim que a aplicação for criada. 

    Após a criação da app, o portal LUIS mostra a lista **de Intenções** com a intenção `None` já criada para si. Agora tem uma aplicação vazia. 
    
    > [!div class="mx-imgBorder"]
    > ![lista de intenções com nenhuma intenção criada sem declarações de exemplo.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Outras ações disponíveis

A barra de ferramentas de contexto fornece outras ações:

* Aplicação de renome
* Importação de ficheiros com `.lu` ou `.json`
* Aplicativo de exportação como `.lu` (para [LUDown),](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)`.json`ou `.zip` (para [recipiente LUIS)](luis-container-howto.md)
* Importador de registos finais de contentores, para rever as declarações de pontos finais
* Registos finais de exportação, como `.csv`, para análise offline
* Excluir app

## <a name="next-steps"></a>Passos seguintes

Se o design da sua aplicação incluir deteção de [intenções, crie novas intenções](luis-how-to-add-intents.md)e adicione expressões de exemplo. Se o design da sua aplicação for apenas extração de dados, adicione declarações de exemplo à intenção None, em seguida, [crie entidades](luis-how-to-add-example-utterances.md), e rotule as palavras de exemplo com essas entidades. 
