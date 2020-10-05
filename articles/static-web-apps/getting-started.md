---
title: 'Quickstart: Construir a sua primeira aplicação web estática com as Aplicações Web Estáticas Azure'
description: Aprenda a construir um website Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: db3836e6171d187539b8615efcb5ab782c368020
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88752595"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Quickstart: Construir a sua primeira aplicação web estática

A Azure Static Web Apps publica um site para um ambiente de produção construindo aplicações a partir de um repositório GitHub. Neste arranque rápido, implementa uma aplicação web para aplicações Web Azure Static utilizando a extensão Visual Studio Code.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- [Conta GitHub](https://github.com)
- [Conta Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extensão de Aplicativos Web Estáticos Azure para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Em seguida, abra o Código do Estúdio Visual e vá ao **Arquivo > Pasta Aberta** para abrir o repositório que acabou de clonar para a sua máquina no editor.

## <a name="create-a-static-web-app"></a>Crie uma aplicação web estática

1. No Código do Estúdio Visual, selecione o logótipo Azure na Barra de Atividade para abrir a janela de extensões Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logotipo Azure":::

    > [!NOTE]
    > Azure e GitHub assinam são necessários. Se ainda não tiver assinado no Azure e no GitHub do Visual Studio Code, a extensão irá levá-lo a iniciar seducação em ambos durante o processo de criação.

1. Coloque o rato sobre a etiqueta _Static Web Apps_ e selecione o sinal de **mais**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Logotipo Azure":::

1. O paladar de comando abre no topo do editor e pede-lhe para nomear a sua candidatura.

    Digite **a minha primeira aplicação estática** web e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Logotipo Azure":::

1. Selecione o ramo **principal** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Logotipo Azure":::

1. Selecione **/** como a localização do código de aplicação e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Logotipo Azure":::

1. A extensão está à procura da localização da API na sua aplicação. Este artigo não implementa uma API.

    Selecione **Skip por agora** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Logotipo Azure":::

1. Selecione o local onde os ficheiros são construídos para produção na sua aplicação.

    # <a name="no-framework"></a>[Sem Enquadramento](#tab/vanilla-javascript)

    Limpe a caixa e **pressione Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Logotipo Azure":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Tipo **dist/angular-básico** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Logotipo Azure":::

    # <a name="react"></a>[React](#tab/react)

    Tipo **de construção** e **pressione Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Logotipo Azure":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Escreva **dist** e **pressione Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Logotipo Azure":::

    ---

1. Selecione uma localização mais próxima e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Logotipo Azure":::

1. Uma vez criada a aplicação, uma notificação de confirmação é mostrada no Código do Estúdio Visual.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Logotipo Azure":::

1. Na janela Visual Studio Code Explorer, volte à secção _de Aplicações Web Estáticas_ e clique com o botão direito na **Produção** e selecione **Abrir no Portal** para ver a aplicação no portal Azure.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Logotipo Azure":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar a utilizar esta aplicação, pode eliminar a instância Azure Static Web Apps através da extensão.

Na janela Visual Studio Code Explorer, volte à secção _de Aplicações Web Estáticas_ e clique com o botão direito na **minha primeira aplicação estática** e selecione **Delete**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Logotipo Azure":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
