---
title: 'Tutorial: Publicar um site da VuePress para a Azure Static Web Apps'
description: Este tutorial mostra-lhe como implementar uma aplicação VuePress para aplicações Web Estáticas Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 72b1bd4d46b0b04364b25a8460361d6a008d42a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250135"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Tutorial: Publicar um site da VuePress para visualização de aplicações web estáticas Azure

Este artigo demonstra como criar e implementar uma aplicação web [VuePress](https://vuepress.vuejs.org/) para [Azure Azure Static Web Apps](overview.md). O resultado final é uma nova aplicação Azure Static Web Apps com as ações associadas do GitHub Que lhe dão controlo sobre como a aplicação é construída e publicada.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar uma aplicação VuePress
> - Configurar uma Azure Static Web Apps
> - Implementar a aplicação VuePress para a Azure

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Se não tiver uma, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/)
- Uma conta do GitHub. Se não tiver uma, pode [criar uma conta gratuitamente.](https://github.com/join)
- [Node.js](https://nodejs.org) instalados.

## <a name="create-a-vuepress-app"></a>Criar uma app VuePress

Criar uma aplicação VuePress a partir da Interface da Linha de Comando (CLI):

1. Crie uma nova pasta para a aplicação VuePress.

   ```bash
   mkdir static-site
   ```

1. Adicione uma _README.md_ arquivar a pasta.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Inicialize a _package.jsarquivada._

   ```bash
   npm init -y
   ```

1. Adicione a VuePress como um `devDependency` .

   ```bash
   npm install --save-dev vuepress
   ```

1. Abra a _package.jsficheiro num_ editor de texto e adicione um comando de construção à [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) secção.

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Crie um ficheiro _.gitignore_ para excluir a pasta de módulos de _ \_ nó._

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Inicialize um git repo.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Empurre a sua aplicação para o GitHub

Você precisa de um repositório no GitHub para ligar a Azure Static Web Apps. Os passos seguintes mostram-lhe como criar um repositório para o seu site.

1. Crie um repo GitHub em branco (não crie um README) a partir do [https://github.com/new](https://github.com/new) nome **vuepress-static-app**.

1. Adicione o repositório GitHub como um controlo remoto ao seu repo local. Certifique-se de adicionar o seu nome de utilizador GitHub no lugar do `<YOUR_USER_NAME>` espaço reservado no comando seguinte.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Empurre o seu repo local até GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Implemente a sua aplicação web

Os passos seguintes mostram-lhe como criar uma nova aplicação estática de Web Apps e implantá-la num ambiente de produção.

### <a name="create-the-application"></a>Criar a aplicação

1. Navegue até ao [portal Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**.
1. Procure **Aplicações Web Estáticas**
1. Clique em **Aplicações Web Estáticas (Pré-visualização)**
1. Clique em **Criar**

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Criar uma Web Apps estática (pré-visualização) no portal":::

1. Para **Subscrição**, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. No _grupo de recursos_, selecione **New**. No _nome do grupo de novos recursos,_ insira a **aplicação vuepress-static-está** e selecione **OK**.

1. Em seguida, um nome para a sua aplicação na caixa **Nome.** Os caracteres válidos `a-z` `A-Z` incluem, e `0-9` `-` .

1. Para _a Região_, selecione uma região disponível perto de si.

1. Para _SKU_, selecione **Grátis**.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Criar uma Web Apps estática (pré-visualização) no portal":::

1. Clique **no botão Iniciar sin com GitHub.**

1. Selecione a **Organização** sob a qual criou o repo.

1. Selecione a **aplicação vuepress-static como** o _Repositório_ .

1. Para o _Branch_ selecione **master**.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Criar uma Web Apps estática (pré-visualização) no portal":::

### <a name="build"></a>Compilar

Em seguida, adiciona definições de configuração que o processo de construção utiliza para construir a sua aplicação. As seguintes definições configuram o ficheiro de fluxo de trabalho gitHub Action.

1. Clique no botão **Seguinte: Compilar>** para editar a configuração de compilação

1. Definir _a localização da aplicação_ para **/** .

1. Definir _a localização do artefacto da App_ para **.vuepress/dist**.

Um valor para _a localização_ da API não é necessário, uma vez que não está a implementar uma API neste momento.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Criar uma Web Apps estática (pré-visualização) no portal":::

### <a name="review-and-create"></a>Rever e criar

1. Clique no botão **'Rever + Criar'** para verificar se todos os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação das Aplicações Web Estáticas Azure e providenciar uma Ação GitHub para implementação.

1. Assim que a implementação estiver concluída clique, **aceda ao recurso**.

1. No ecrã de recursos, clique no link _URL_ para abrir a aplicação implementada. Pode ter de esperar um minuto ou dois para que a Ação GitHub esteja concluída.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Criar uma Web Apps estática (pré-visualização) no portal":::

### <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
