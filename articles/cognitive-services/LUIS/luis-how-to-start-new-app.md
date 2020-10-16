---
title: Criar uma nova app - LUIS
titleSuffix: Azure Cognitive Services
description: Crie e gere as suas aplicações na página web do Understanding language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2020
ms.openlocfilehash: fcc803e87b2efd3e94b90eb17258ab3bb156359f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541442"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar uma nova app LUIS no portal LUIS
Há algumas formas de criar uma aplicação LUIS. Pode criar uma aplicação LUIS no portal LUIS, ou através das [APIs](developer-reference-resource.md)de autoria do LUIS.

## <a name="using-the-luis-portal"></a>Usando o portal LUIS

Pode criar uma nova aplicação no portal de várias formas:

* Comece com uma app vazia e crie intenções, expressões e entidades.
* Comece com uma aplicação vazia e adicione um [domínio pré-construído.](luis-how-to-use-prebuilt-domains.md)
* Importe uma aplicação LUIS de um `.lu` ou ficheiro que já `.json` contenha intenções, declarações e entidades.

## <a name="using-the-authoring-apis"></a>Utilizando as APIs de autoria
Pode criar uma nova aplicação com as APIs de autoria de algumas formas:

* [Adicionar aplicação](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - comece com uma app vazia e crie intenções, expressões e entidades.
* [Adicionar aplicação pré-construída](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - comece com um domínio pré-construído, incluindo intenções, expressões e entidades.


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Criar nova app no LUIS

1. Na página **My Apps,** selecione a sua **Subscrição**e  **o recurso de autoria** e, em **seguida, + Criar**. 

> [!div class="mx-imgBorder"]
> ![Lista de aplicações LUIS](./media/create-app-in-portal.png)

1. Na caixa de diálogo, insira o nome da sua aplicação, tal como `Pizza Tutorial` .

    ![Criar novo diálogo de aplicativos](./media/create-pizza-tutorial-app-in-portal.png)

1. Escolha a sua cultura de aplicação e, em seguida, selecione **Feito**. A descrição e o recurso de previsão são opcionais neste momento. Pode definir a qualquer momento na secção **Gerir** o portal.

    > [!NOTE]
    > A cultura não pode ser alterada assim que a aplicação for criada.

    Após a criação da app, o portal LUIS mostra a lista **de Intenções** com a `None` intenção já criada para si. Agora tem uma aplicação vazia.

    > [!div class="mx-imgBorder"]
    > ![Lista de intenções sem nenhuma intenção criada sem palavras de exemplo.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available-on-my-apps-page"></a>Outras ações disponíveis na página My Apps

A barra de ferramentas de contexto fornece outras ações:

* Aplicativo de renome
* Importar a partir de ficheiros usando `.lu` ou `.json`
* App de exportação como `.lu` (para [LUDown),](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown) `.json` ou `.zip` (para [o recipiente LUIS)](luis-container-howto.md)
* Registos de pontos finais de contentores de importação, para rever as expressões de ponto final
* Registos de pontos finais de exportação, como um `.csv` , para análise offline
* Excluir app

## <a name="next-steps"></a>Passos seguintes

Se o design da sua aplicação incluir deteção de [intenções, crie novas intenções](luis-how-to-add-intents.md)e adicione palavras de exemplo. Se o design da sua aplicação for apenas a extração de dados, adicione palavras de exemplo à intenção de Zero, em seguida, [crie entidades](luis-how-to-add-example-utterances.md), e rotule as expressões de exemplo com essas entidades.
