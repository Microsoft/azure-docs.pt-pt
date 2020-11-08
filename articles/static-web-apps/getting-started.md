---
title: 'Quickstart: Construir a sua primeira aplicação web estática com as Aplicações Web Estáticas Azure'
description: Aprenda a construir um website Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: a78f74b4aadd26af141ed84ca99a092693f56af5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369567"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Quickstart: Construir a sua primeira aplicação web estática

A Azure Static Web Apps publica um site para um ambiente de produção construindo aplicações a partir de um repositório GitHub. Neste arranque rápido, implementa uma aplicação web para aplicações Web Azure Static utilizando a extensão Visual Studio Code.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- Conta do [GitHub](https://github.com)
- [Conta Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extensão de Aplicativos Web Estáticos Azure para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Em seguida, abra o Código do Estúdio Visual e vá ao **Arquivo > Pasta Aberta** para abrir o repositório que acabou de clonar para a sua máquina no editor.

## <a name="create-a-static-web-app"></a>Criar uma aplicação Web estática

1. No Visual Studio Code, selecione o logótipo do Azure na Barra de Atividade para abrir a janela de extensões do Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logótipo do Azure":::

    > [!NOTE]
    > É preciso iniciar sessão no Azure e no GitHub. Se ainda não tiver sessão iniciada no Azure e no GitHub a partir do Visual Studio Code, a extensão irá pedir-lhe que a inicie durante o processo de criação.

1. Coloque o cursor do rato sobre a etiqueta _Aplicações Web estáticas_ e selecione o **sinal de adição**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nome da aplicação":::

1. O paladar de comando abre no topo do editor e pede-lhe para nomear a sua candidatura.

    Escreva **a-minha-primeira-app-web-estatica** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Criar Aplicação Web Estática":::

1. Selecione o ramo **principal** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Nome do ramo":::

1. Selecione **/** como a localização do código de aplicação e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Localização do código da aplicação":::

1. A extensão está à procura da localização da API na sua aplicação. Este artigo não implementa uma API.

    Selecione **Ignorar por agora** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Localização da API":::

1. Selecione a localização em que os ficheiros são compilados para produção na sua aplicação.

    # <a name="no-framework"></a>[Sem Enquadramento](#tab/vanilla-javascript)

    Limpe a caixa e **pressione Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Caminho de ficheiros de aplicativos":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Tipo **dist/angular-básico** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Caminho dos ficheiros da aplicação Angular":::

    # <a name="react"></a>[React](#tab/react)

    Escreva **build** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Caminho dos ficheiros da aplicação React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Escreva **dist** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Caminho dos ficheiros da aplicação Vue":::

    ---

1. Selecione uma localização mais próxima de si e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Localização do recurso":::

1. Assim que a aplicação for criada, é apresentada uma notificação de confirmação no Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Confirmação de criação":::

1. Na janela Visual Studio Code Explorer, navegue até ao nó que tem o seu nome de subscrição e expanda-o. Por favor, note que pode levar alguns minutos para a colocação estar concluída. Em seguida, volte à secção de Aplicações Web Estáticas e selecione o nome da sua aplicação e, em seguida, clique com o direito na minha primeira aplicação estática e selecione Open in Portal para ver a aplicação no portal Azure.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Abrir portal":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar a utilizar esta aplicação, pode eliminar a instância Azure Static Web Apps através da extensão.

Na janela Visual Studio Code Explorer, volte à secção _de Aplicações Web Estáticas_ e clique com o botão direito na **minha primeira aplicação estática** e selecione **Delete**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Excluir app":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
