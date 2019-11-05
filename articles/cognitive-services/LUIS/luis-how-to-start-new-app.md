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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 227efcdbcb7d8e776dd77b38c5d1dedd54d71b6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500289"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar um novo aplicativo LUIS no portal do LUIS
Há algumas maneiras de criar um aplicativo LUIS. Você pode criar um aplicativo LUIS no portal do [Luis](https://www.luis.ai) ou por meio das [APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)de criação do Luis.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="using-the-luis-portal"></a>Usando o portal do LUIS

Você pode criar um novo aplicativo no portal do LUIS de várias maneiras:

* Comece com um aplicativo vazio e crie intenções, declarações e entidades.
* Comece com um aplicativo vazio e adicione um [domínio predefinido](luis-how-to-use-prebuilt-domains.md).
* Importe um aplicativo LUIS de um arquivo JSON que já contenha intenções, declarações e entidades.

## <a name="using-the-authoring-apis"></a>Usando as APIs de criação
Você pode criar um novo aplicativo com as APIs de criação de duas maneiras:

* [Comece](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) com um aplicativo vazio e crie intenções, declarações e entidades.
* [Comece](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) com um domínio predefinido.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Criar novo aplicativo no LUIS

1. Na página **meus aplicativos** , selecione **+ criar**.

    ![Lista de aplicações LUIS](./media/luis-create-new-app/apps-list.png)


2. Na caixa de diálogo, nomeie o aplicativo como "TravelAgent".

    ![Caixa de diálogo Criar novo aplicativo](./media/luis-create-new-app/create-app.png)

3. Escolha a cultura do aplicativo (para o aplicativo TravelAgent, escolha inglês) e, em seguida, selecione **concluído**. 

    > [!NOTE]
    > A cultura não pode ser alterada assim que a aplicação for criada. 

## <a name="import-an-app-from-file"></a>Importar um aplicativo do arquivo

1. Na página **meus aplicativos** , selecione **importar novo aplicativo**.
1. Na caixa de diálogo pop-up, selecione um arquivo JSON de aplicativo válido e, em seguida, selecione **concluído**.

### <a name="import-errors"></a>Erros de importação

Os erros possíveis são: 

* Já existe um aplicativo com esse nome. Para corrigir isso, reimporte o aplicativo e defina o **nome opcional** como um novo nome. 

## <a name="export-app-for-backup"></a>Exportar aplicativo para backup

1. Na página **meus aplicativos** , selecione **Exportar**.
1. Selecione **exportar como JSON**. Seu navegador baixa a versão ativa do aplicativo.
1. Adicione esse arquivo ao sistema de backup para arquivar o modelo.

## <a name="export-app-for-containers"></a>Exportar aplicativo para contêineres

1. Na página **meus aplicativos** , selecione **Exportar**.
1. Selecione **exportar como contêiner** e, em seguida, selecione qual slot publicado (produção ou estágio) você deseja exportar.
1. Use esse arquivo com o [contêiner Luis](luis-container-howto.md). 

    Se você estiver interessado em exportar um modelo treinado mas ainda não publicado para usar com o contêiner LUIS, vá para a página **versões** e exporte a partir daí. 

## <a name="delete-app"></a>Excluir aplicativo

1. Na página **meus aplicativos** , selecione os três pontos (...) no final da linha do aplicativo.
1. Selecione **excluir** no menu.
1. Selecione **OK** na janela de confirmação.

## <a name="next-steps"></a>Passos seguintes

Sua primeira tarefa no aplicativo é [Adicionar tentativas](luis-how-to-add-intents.md).
