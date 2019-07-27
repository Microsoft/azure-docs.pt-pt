---
title: Criar um novo aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: Criar e gerir as suas aplicações na página de Web de compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 7f9809fde088a03d4b20b5c739253f446c7a84b6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563618"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar um novo aplicativo LUIS no portal do LUIS
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

## <a name="delete-app"></a>Eliminar aplicação

1. Na página **meus aplicativos** , selecione os três pontos (...) no final da linha do aplicativo.
1. Selecione **excluir** no menu.
1. Selecione **OK** na janela de confirmação.

## <a name="next-steps"></a>Passos Seguintes

Sua primeira tarefa na aplicação é [adicionar intenções](luis-how-to-add-intents.md).
