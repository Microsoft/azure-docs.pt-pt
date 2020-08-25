---
title: 'Quickstart: Construir a sua primeira aplicação web estática com aplicações web estáticas Azure usando o portal Azure'
description: Aprenda a construir uma instância de Aplicações Web Estáticas Azure com o portal Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: e0b78c5e053c5668fbebd8ebaac91a90aa2b364f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752928"
---
# <a name="quickstart-building-your-first-static-web-app-in-the-azure-portal"></a>Quickstart: Construir a sua primeira aplicação web estática no portal Azure

A Azure Static Web Apps publica um site para um ambiente de produção construindo aplicações a partir de um repositório GitHub. Neste arranque rápido, implementa uma aplicação web para aplicações Web Estáticas Azure usando o portal.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- [Conta GitHub](https://github.com)
- [Conta Azure](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Crie uma aplicação web estática

Agora que o repositório foi criado, pode criar uma aplicação web estática a partir do portal Azure.

1. Navegue até ao [portal Azure](https://portal.azure.com)
1. Selecione **Criar um recurso**
1. Procure **Aplicações Web Estáticas**
1. Selecione **aplicativos web estáticos (pré-visualização)**
1. Selecione **Criar**

No separador _Basics,_ comece por configurar a sua nova aplicação e ligá-la a um repositório GitHub.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Separador básico":::

1. Selecione a sua _subscrição Azure_
1. Selecione ou crie um novo _Grupo de Recursos_
1. Nomeie a aplicação **my-first-static-web-app**.
      1. Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`.
1. Selecione uma _região_ mais próxima
1. Selecione o _SKU_ **gratuito**
1. Selecione o **'Iniciar s-in' com** o botão GitHub e autentica-se com o GitHub

Depois de assinar com o GitHub, insira a informação do repositório.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Detalhes do repositório":::

1. Selecione a sua _Organização_ Preferida
1. Selecione **a minha primeira web-web-static-app** a partir do _repositório_ drop-down
1. Selecione **mestre** do _ramo_ drop-down
1. Selecione o **Seguinte: Construa >** botão para editar a configuração de construção

:::image type="content" source="media/getting-started-portal/next-build-button.png" alt-text="Próximo botão de construção":::

> [!NOTE]
> Se não vir nenhum repositório, poderá ter de autorizar aplicações web estáticas Azure no GitHub. Navegue no seu repositório GitHub e vá a **Definições > Aplicações > Aplicações OAuth Autorizadas**, selecione **Azure Static Web Apps**, e, em seguida, selecione **Grant**. Para repositórios de organização, você deve ser um proprietário da organização para conceder as permissões.

1. No separador _'Construir',_ adicione detalhes de configuração específicos à sua estrutura frontal preferida.

    # <a name="no-framework"></a>[Sem Enquadramento](#tab/vanilla-javascript)

    - Limpar o valor predefinido na caixa _de localização_ da App
    - Limpe o valor padrão da caixa de _localização Api_
    - Limpe o valor padrão da caixa de _localização_ do artefacto da App

    # <a name="angular"></a>[Angular](#tab/angular)

    - Limpar o valor predefinido na caixa _de localização_ da App
    - Limpe o valor padrão da caixa de _localização Api_
    - Tipo **dist/angular-básico** na caixa de _localização_ do artefacto da App

    # <a name="react"></a>[React](#tab/react)

    - Limpar o valor predefinido da caixa _de localização da App_
    - Limpe o valor padrão da caixa de _localização Api_
    - Tipo **construir** na caixa de _localização_ do artefacto da App

    # <a name="vue"></a>[Vue](#tab/vue)

    - Limpar o valor predefinido da caixa _de localização da App_
    - Limpe o valor padrão da caixa de _localização Api_
    - Tipo **dist** na caixa de _localização_ do artefacto da App

    ---

1. Selecione **Rever + criar**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Rever criar botão":::

    > [!NOTE]
    > Pode editar o [ficheiro workflow](github-actions-workflow.md) para alterar estes valores depois de criar a aplicação.

1. Selecione **Criar**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Botão Criar":::

1. Selecione **Ir para recurso**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Botão Ir para recurso":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar a utilizar esta aplicação, pode eliminar a instância Azure Static Web Apps através dos seguintes passos:

1. Abra o [portal do Azure](https://portal.azure.com)
1. Procure **a minha primeira web-web-app** a partir da barra de pesquisa de topo
1. Selecione no nome da aplicação
1. Selecione no botão **Eliminar**
1. Selecione **Sim** para confirmar a ação de eliminação

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
