---
title: 'Tutorial: Publique um site da VuePress para aplicações web estáticas do Azure'
description: Este tutorial mostra-lhe como implementar uma aplicação VuePress para Aplicações Web Estáticas Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: bd9eaad0c141eda815da159e3c13d6c51f5e6200
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599740"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Tutorial: Publique um site da VuePress para a pré-visualização de aplicações da Web estática do Azure

Este artigo demonstra como criar e implementar uma aplicação web [VuePress](https://vuepress.vuejs.org/) para [aplicações web estáticas Azure Azure.](overview.md) O resultado final é uma nova aplicação azure static Web Apps com as ações gitHub associadas que lhe dão controlo sobre como a aplicação é construída e publicada.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> - Criar uma aplicação VuePress
> - Configurar uma Web Apps Estátáticas Azure
> - Implementar a app VuePress para o Azure

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Se não tiver uma, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/)
- Uma conta do GitHub. Se não tiver uma, pode [criar uma conta gratuitamente.](https://github.com/join)
- Ter o [Node.js](https://nodejs.org) instalado.

## <a name="create-a-vuepress-app"></a>Criar uma App VuePress

Criar uma aplicação VuePress a partir da Interface da Linha de Comando (CLI):

1. Crie uma nova pasta para a aplicação VuePress.

   ```bash
   mkdir static-site
   ```

1. Adicione um _README.md_ arquivar a pasta.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Inicialize o ficheiro _package.json._

   ```bash
   npm init -y
   ```

1. Adicione vuePress como `devDependency` um .

   ```bash
   npm install --save-dev vuepress
   ```

1. Abra o ficheiro _package.json_ num editor de texto e adicione um comando de construção à [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) secção.

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Crie um ficheiro _.gitignore_ para excluir a pasta dos _ \_ módulos_ do nó.

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

Você precisa de um repositório no GitHub para ligar a Web Apps Estáticas Azure. Os seguintes passos mostram-lhe como criar um repositório para o seu site.

1. Crie um repo GitHub em branco (não crie uma README) a partir da [https://github.com/new](https://github.com/new) **aplicação vuepress-estática**.

1. Adicione o repositório GitHub como um controlo remoto ao seu repo local. Certifique-se de adicionar o seu nome de utilizador GitHub no lugar do `<YOUR_USER_NAME>` espaço reservado no seguinte comando.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Empurre o seu repo local até ao GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Implemente a sua aplicação web

Os seguintes passos mostram-lhe como criar uma nova aplicação de Aplicações Web Estáticas e implantá-la para um ambiente de produção.

### <a name="create-the-application"></a>Criar a aplicação

1. Navegue para o [portal do Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**
1. Pesquisa de **aplicações web estáticas**
1. Clique em **aplicações web estáticas (pré-visualização)**
1. Clique em **Criar**

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Criar uma Web Apps Estática (Pré-visualização) no portal":::

1. Para **Subscrição**, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. No _grupo Recursos,_ selecione **New**. Em novo nome de _grupo de recursos,_ introduza a **aplicação vuepress-estática** e selecione **OK**.

1. Em seguida, forneça um nome globalmente único para a sua aplicação na caixa **Nome.** Os caracteres válidos `a-z` `A-Z` incluem, e `0-9` `-` . Este valor é usado como prefixo URL para a sua aplicação estática no formato de `https://<APP_NAME>.azurestaticapps.net` .

1. Para _região,_ selecione uma região disponível perto de si.

1. Para _SKU,_ selecione **Free**.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Detalhes preenchidos":::

1. Clique no **'Iniciar sessão' com** o botão GitHub.

1. Selecione a **Organização** sob a qual criou o repo.

1. Selecione a **aplicação vuepress-estática** como _repositório_ .

1. Para o _Branch_ selecione **master**.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Informação completa do GitHub":::

### <a name="build"></a>Compilação

Em seguida, adiciona definições de configuração que o processo de construção utiliza para construir a sua aplicação. As seguintes definições configuram o ficheiro de fluxo de trabalho GitHub Action.

1. Clique no **seguinte: Construa >** botão para editar a configuração de construção

1. Definir a localização da _aplicação_ para **/** .

1. Definir a localização do artefacto da _aplicação_ para **.vuepress/dist**.

Um valor para a localização da _API_ não é necessário, uma vez que não está a implementar uma API neste momento.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Construir Definições":::

### <a name="review-and-create"></a>Rever e criar

1. Clique no botão **Rever + Criar** para verificar se os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação das Web Apps Estáticas Do Azure e fornecer uma Ação GitHub para implementação.

1. Uma vez que a implementação esteja concluída clique, **vá para o recurso**.

1. No ecrã de recursos, clique no link _URL_ para abrir a sua aplicação implantada. Pode ter de esperar um minuto ou dois para que a Ação GitHub esteja concluída.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Aplicação implantada":::

### <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
