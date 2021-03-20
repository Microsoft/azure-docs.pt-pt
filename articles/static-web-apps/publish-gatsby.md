---
title: 'Tutorial: Publicar um site gatsby para Azure Static Web Apps'
description: Este tutorial mostra-lhe como implementar uma aplicação Gatsby para aplicações web estáticas Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4430ed34858077b13b4fec69756c1c7e9f3ef7ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "100652386"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Tutorial: Publicar um site gatsby para pré-visualização de aplicações web estáticas Azure

Este artigo demonstra como criar e implementar uma aplicação web [Gatsby](https://gatsbyjs.org) para [Azure Static Web Apps](overview.md). O resultado final é um novo site estático de Aplicações Web (com as ações associadas do GitHub) que lhe dão controlo sobre como a aplicação é construída e publicada.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar uma aplicação do Gatsby
> - Configurar um site de aplicações web estáticas Azure
> - Implementar a app Gatsby para Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Se não tiver uma, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/)
- Uma conta do GitHub. Se não tiver uma, pode [criar uma conta gratuitamente.](https://github.com/join)
- [Node.js](https://nodejs.org) instalados.

## <a name="create-a-gatsby-app"></a>Criar uma App Gatsby

Criar uma aplicação Gatsby utilizando a Interface da Linha de Comando Gatsby (CLI):

1. Abrir um terminal
1. Utilize a ferramenta [npx](https://www.npmjs.com/package/npx) para criar uma nova aplicação com o Gatsby CLI. Esta operação poderá demorar alguns minutos.

   ```bash
   npx gatsby new static-web-app
   ```

1. Navegue para a app recém-criada

   ```bash
   cd static-web-app
   ```

1. Inicialize um git repo

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Empurre a sua aplicação para o GitHub

Precisa de um repositório no GitHub para criar um novo recurso Azure Static Web Apps.

1. Crie um repositório GitHub em branco (não crie um README) a partir de [https://github.com/new](https://github.com/new) chamada **gatsby-static-web-app**.

1. Em seguida, adicione o repositório GitHub que acabou de criar como um controlo remoto para o seu repo local. Certifique-se de adicionar o seu nome de utilizador GitHub no lugar do `<YOUR_USER_NAME>` espaço reservado no comando seguinte.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. Empurre o seu repositório local até GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Implemente a sua aplicação web

Os passos seguintes mostram-lhe como criar uma nova aplicação estática do site e implantá-la para um ambiente de produção.

### <a name="create-the-application"></a>Criar a aplicação

1. Navegue até ao [portal Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**.
1. Procure **Aplicações Web Estáticas**
1. Clique em **Aplicações Web Estáticas (Pré-visualização)**
1. Clique em **Criar**

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Criar uma Web Apps estática (pré-visualização) no portal":::

1. Para _Subscrição_, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. No _grupo de recursos_, selecione **New**. Em _Novo nome de grupo de recursos,_ **insira gatsby-static-web-app** e selecione **OK**.

1. Em seguida, um nome para a sua aplicação na caixa **Nome.** Os caracteres válidos `a-z` `A-Z` incluem, e `0-9` `-` .

1. Para _a Região_, selecione uma região disponível perto de si.

1. Para _SKU_, selecione **Grátis**.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Detalhes preenchidos":::

1. Clique **no botão Iniciar sin com GitHub.**

1. Selecione a **Organização** sob a qual criou o repositório.

1. Selecione a **aplicação gatsby-static-web como** o _Repositório_ .

1. Para o _Ramo_ selecione **principal**.

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Informação completa do GitHub":::

### <a name="build"></a>Compilar

Em seguida, adicione as definições de configuração que o processo de construção utiliza para construir a sua aplicação.

1. Clique no botão **Seguinte: Compilar >** para editar a configuração de compilação

1. Para configurar as definições do passo em GitHub Actions, defina a _localização_ da App para **/** .

1. Definir _a localização do artefacto da App_ ao **público.**

   Um valor para _a localização_ da API não é necessário, uma vez que não está a implementar uma API neste momento.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Configurações de construção":::

### <a name="review-and-create"></a>Rever e criar

1. Clique no botão **'Rever + Criar'** para verificar se todos os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação da App Service Static Web App e providenciar uma Ação GitHub para implementação.

1. Assim que a implementação estiver concluída clique, **aceda ao recurso**.

1. No ecrã de recursos, clique no link _URL_ para abrir a aplicação implementada. Pode ter de esperar um minuto ou dois para que a Ação GitHub esteja concluída.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Aplicação implantada":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
