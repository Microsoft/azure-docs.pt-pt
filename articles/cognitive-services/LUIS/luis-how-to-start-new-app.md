---
title: Criar uma nova app - LUIS
titleSuffix: Azure Cognitive Services
description: Crie e gerencie as suas aplicações na página da Compreensão da Língua (LUIS).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220833"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar uma nova app LUIS no portal LUIS
Existem algumas formas de criar uma app LUIS. Pode criar uma aplicação LUIS no portal LUIS, ou através da AUTORA DA [LUIS.](developer-reference-resource.md)

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Usando o portal LUIS

Pode criar uma nova aplicação no portal de pré-visualização de várias formas:

* Comece com uma app vazia e crie intençãos, expressões e entidades.
* Comece com uma aplicação vazia e adicione um [domínio pré-construído.](luis-how-to-use-prebuilt-domains.md)
* Importar uma aplicação `.lu` `.json` LUIS de um ou arquivo que já contenha intenções, declarações e entidades.

## <a name="using-the-authoring-apis"></a>Utilização das APIs de autoria
Pode criar uma nova aplicação com as APIs de autoria de várias maneiras:

* [Adicione a aplicação](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - comece com uma app vazia e crie intençãos, expressões e entidades.
* [Adicione aplicação pré-construída](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - comece com um domínio pré-construído, incluindo intenções, expressões e entidades.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Criar nova app no LUIS

1. Na página **Minhas Apps,** selecione a sua subscrição e autor de recursos **então + Criar**. Se estiver a usar a chave de teste gratuita, aprenda a criar um recurso de [autoria.](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)

    ![Lista de aplicações LUIS](./media/create-app-in-portal.png)


1. Na caixa de diálogo, introduza o nome `Pizza Tutorial`da sua aplicação, como .

    ![Criar novo diálogo de aplicativos](./media/create-pizza-tutorial-app-in-portal.png)

1. Escolha a sua cultura de aplicação e, em seguida, selecione **Done**. A descrição e o recurso de previsão são opcionais neste momento. Pode definir então a qualquer momento na secção **Gerir** do portal.

    > [!NOTE]
    > A cultura não pode ser alterada assim que a aplicação for criada. 

    Depois de a app ser criada, o portal `None` LUIS mostra a lista **de Intenções** com a intenção já criada para si. Agora tem uma aplicação vazia. 
    
    > [!div class="mx-imgBorder"]
    > ![Lista de intenções com nenhuma intenção criada sem pronunciamentos de exemplo.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Outras ações disponíveis

A barra de ferramentas de contexto fornece outras ações:

* Aplicação de renome
* Importar a `.lu` partir de ficheiros ou`.json`
* Aplicativo de `.lu` exportação como `.json`(para `.zip` [LUDown),](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)ou (para [recipiente LUIS)](luis-container-howto.md)
* Importador de registos finais de contentores, para rever as declarações de pontos finais
* Registos finais de `.csv`exportação, como a, para análise offline
* Excluir app

## <a name="next-steps"></a>Passos seguintes

Se o design da sua aplicação incluir deteção de [intenções, crie novas intenções](luis-how-to-add-intents.md)e adicione expressões de exemplo. Se o design da sua aplicação for apenas extração de dados, adicione declarações de exemplo à intenção None, em seguida, [crie entidades](luis-how-to-add-example-utterances.md), e rotule as palavras de exemplo com essas entidades. 
