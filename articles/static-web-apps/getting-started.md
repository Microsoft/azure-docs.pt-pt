---
title: 'Quickstart: Construir o seu primeiro site estático com as Aplicações Web Estáticas Azure'
description: Aprenda a implementar um site estático para Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: fb874c25ab688cc5e6723d1023157b8acd9478b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483854"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Quickstart: Construir o seu primeiro site estático com aplicações web estáticas Azure

A Azure Static Web Apps publica um website construindo aplicações a partir de um repositório de código. Neste arranque rápido, implementa uma aplicação para aplicações Web Estáticas Azure usando a extensão Visual Studio Code.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- Conta do [GitHub](https://github.com)
- [Conta Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Aplicações Web Estáticas do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Instalar o Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Em seguida, abra o Código do Estúdio Visual e vá ao **Arquivo > Folder Aberto** para abrir o repositório que clonou para a sua máquina no editor.

## <a name="create-a-static-web-app"></a>Criar uma aplicação Web estática

1. No Visual Studio Code, selecione o logótipo do Azure na Barra de Atividade para abrir a janela de extensões do Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logótipo do Azure":::

    > [!NOTE]
    > É preciso iniciar sessão no Azure e no GitHub. Se ainda não tiver sessão iniciada no Azure e no GitHub a partir do Visual Studio Code, a extensão irá pedir-lhe que a inicie durante o processo de criação.

1. Sob a etiqueta _Static Web Apps,_ selecione o **sinal de mais**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nome da aplicação":::

1. O paladar de comando abre no topo do editor e pede-lhe para nomear a sua candidatura.

    Escreva **a-minha-primeira-app-web-estatica** e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Criar Aplicação Web Estática":::

1. Selecione as predefinições que correspondem ao seu tipo de aplicação.

    # <a name="no-framework"></a>[Sem Enquadramento](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Predefinições de aplicação: Sem enquadramento":::

    **Insira ./** como a localização dos ficheiros de aplicação.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Localização dos ficheiros de aplicações":::

    Selecione **Skip por enquanto** como local para a API de Funções Azure.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Localização da API":::

    Insira **./** como o local de saída de construção.

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Localização de saída de construção de aplicações":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Embora exista uma predefinição angular, selecione a opção **Custom** para que possa fornecer uma localização de saída adequada para esta aplicação.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Predefinições de aplicação: Angular":::

    **Insira ./** como a localização dos ficheiros de aplicação.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Localização dos ficheiros de aplicações: Angular":::

    Selecione **Skip por enquanto** como local para a API de Funções Azure.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Localização da API: Angular":::

    Introduza **dist/angular-básico** como o local de saída de construção.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Localização de saída de construção de aplicação: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Predefinições de aplicação: Reagir":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Predefinições de aplicação: Vue":::

    ---

1. Selecione uma localização mais próxima de si e prima **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Localização do recurso":::

1. Assim que a aplicação for criada, é apresentada uma notificação de confirmação no Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Confirmação de criação":::

    Em seguida, clique no botão **Abrir Ações no GitHub**. Esta página mostra-lhe o estado de construção da aplicação.

    Uma vez concluída a Ação GitHub, poderá navegar pelo site publicado.

1. Para ver o website no navegador, clique com o botão direito no projeto na extensão estática de Aplicações Web e selecione **Browse Site**.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Procurar site":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, pode eliminar a instância Azure Static Web Apps através da extensão.

Na janela Visual Studio Code Explorer, volte à secção _de Aplicações Web Estáticas_ e clique com o botão direito na **minha primeira aplicação estática** e selecione **Delete**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Excluir app":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
