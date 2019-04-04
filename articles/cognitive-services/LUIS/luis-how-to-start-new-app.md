---
title: Criar uma nova aplicação
titleSuffix: Language Understanding - Azure Cognitive Services
description: Criar e gerir as suas aplicações na página de Web de compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: 72c4f23f47e0a2c6d9a96dbbe36716bc3ab665f1
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891434"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar uma nova aplicação LUIS no portal do LUIS
Existem duas formas para criar uma aplicação do LUIS. Pode criar uma aplicação LUIS no [LUIS](https://www.luis.ai) portal, ou por meio do LUIS criação [APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

## <a name="using-the-luis-portal"></a>Com o portal de LUIS
Pode criar uma nova aplicação no portal do LUIS de várias formas:

* Começar com uma aplicação vazia e crie objetivos, expressões e entidades.
* Comece com uma aplicação vazia e adicione um [domínio pré-criado](luis-how-to-use-prebuilt-domains.md).
* Importe uma aplicação do LUIS de um ficheiro JSON que já contém intenções, expressões e entidades.

## <a name="using-the-authoring-apis"></a>Com as APIs de criação
Pode criar uma nova aplicação com as APIs de criação de duas formas:

* [Iniciar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) com uma aplicação vazia e crie objetivos, expressões e entidades.
* [Iniciar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) com um domínio pré-criado.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Criar nova aplicação LUIS

1. No **as minhas aplicações** página, selecione **criar nova aplicação**.

    ![Lista de aplicações LUIS](./media/luis-create-new-app/apps-list.png)


2. Na caixa de diálogo, nome de seu aplicativo "TravelAgent".

    ![Criar nova caixa de diálogo da aplicação](./media/luis-create-new-app/create-app.png)

3. Escolha sua cultura de aplicação (para aplicação de TravelAgent, selecione inglês) e, em seguida, selecione **feito**. 

    > [!NOTE]
    > A cultura não pode ser alterada assim que a aplicação for criada. 

## <a name="import-an-app-from-file"></a>Importar uma aplicação a partir do ficheiro

1. No **as minhas aplicações** página, selecione **importar a nova aplicação**.
1. Na caixa de diálogo pop-up, selecione um ficheiro JSON de aplicação válido e, em seguida, selecione **feito**.

### <a name="import-errors"></a>Importar erros

Erros possíveis são: 

* Já existe uma aplicação com esse nome. Importe novamente o aplicativo e defina a **nome opcional** para um novo nome. 

## <a name="export-app"></a>Exportar a aplicação

1. No **as minhas aplicações** página, selecione **importar a nova aplicação**.
1. Na **importar a nova aplicação** caixa de diálogo, selecione o ficheiro JSON da definição da aplicação do LUIS.

## <a name="delete-app"></a>Eliminar aplicação

1. No **as minhas aplicações** , selecione as reticências (...) no final da linha da aplicação.
1. Selecione **eliminar** no menu.
1. Selecione **Ok** na janela de confirmação.

## <a name="next-steps"></a>Passos Seguintes

Sua primeira tarefa na aplicação é [adicionar intenções](luis-how-to-add-intents.md).
