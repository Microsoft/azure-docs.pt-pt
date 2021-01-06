---
title: 'Tutorial: Publicar um site Hugo para Azure Static Web Apps'
description: Saiba como implementar uma aplicação Hugo para aplicações Web Estáticas Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: e49a84f5ac507ac80481313c103701a88934083a
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900778"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Tutorial: Publicar um site Hugo para pré-visualização de aplicações web estáticas Azure

Este artigo demonstra como criar e implementar uma aplicação web [Hugo](https://gohugo.io/) para [Azure Static Web Apps](overview.md). O resultado final é uma nova App Web Estática Azure com ações gitHub associadas que lhe dão controlo sobre como a aplicação é construída e publicada.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> - Criar uma aplicação Hugo
> - Configurar uma Azure Static Web Apps
> - Implementar a app Hugo para Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Se não tiver uma, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/)
- Uma conta do GitHub. Se não tiver uma, pode [criar uma conta gratuitamente.](https://github.com/join)

## <a name="create-a-hugo-app"></a>Criar uma App Hugo

Criar uma aplicação Hugo utilizando a Interface da Linha de Comando Hugo (CLI):

1. Siga o [guia de instalação](https://gohugo.io/getting-started/installing/) para Hugo no seu SISTEMA.

1. Abrir um terminal

1. Executar o Hugo CLI para criar uma nova aplicação.

   ```bash
   hugo new site static-app
   ```

1. Navegue para a aplicação recém-criada.

   ```bash
   cd static-app
   ```

1. Inicialize um git repo.

   ```bash
    git init
   ```

1. Em seguida, adicione um tema ao site instalando um tema como submodule git e, em seguida, especificando-o no ficheiro Hugo config.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Cometa as mudanças.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Empurre a sua aplicação para o GitHub

Você precisa de um repositório no GitHub para ligar a Azure Static Web Apps. Os passos seguintes mostram-lhe como criar um repositório para o seu site.

1. Crie um repo GitHub em branco (não crie um README) a partir do [https://github.com/new](https://github.com/new) nome **hugo-static-app**.

1. Adicione o repositório GitHub como um controlo remoto ao seu repo local. Certifique-se de adicionar o seu nome de utilizador GitHub no lugar do `<YOUR_USER_NAME>` espaço reservado no comando seguinte.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Empurre o seu repo local até GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Implemente a sua aplicação web

Os passos seguintes mostram-lhe como criar uma nova aplicação estática do site e implantá-la para um ambiente de produção.

### <a name="create-the-application"></a>Criar a aplicação

1. Navegue até ao [portal Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**.
1. Procure **Aplicações Web Estáticas**
1. Clique em **Aplicações Web Estáticas (Pré-visualização)**
1. Clique em **Criar**

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Criar um recurso Azure Static Web Apps no portal":::

1. Para **Subscrição**, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. No _grupo de recursos_, selecione **New**. No _nome do grupo de novos recursos,_ insira a **aplicação hugo-estática** e selecione **OK**.

1. Em seguida, um nome para a sua aplicação na caixa **Nome.** Os caracteres válidos `a-z` `A-Z` incluem, e `0-9` `-` .

1. Para _a Região_, selecione uma região disponível perto de si.

1. Para _SKU_, selecione **Grátis**.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Detalhes preenchidos":::

1. Clique **no botão Iniciar sin com GitHub.**

1. Selecione a **Organização** sob a qual criou o repo.

1. Selecione a **aplicação hugo-estática** como _repositório_ .

1. Para o _Branch_ selecione **master**.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Informação completa do GitHub":::

### <a name="build"></a>Compilar

Em seguida, adiciona definições de configuração que o processo de construção utiliza para construir a sua aplicação. As seguintes definições configuram o ficheiro de fluxo de trabalho gitHub Action.

1. Clique no botão **Seguinte: Compilar >** para editar a configuração de compilação

1. Definir _a localização da aplicação_ para **/** .

1. Definir _a localização do artefacto da App_ ao **público.**

   Um valor para _a localização_ da API não é necessário, uma vez que não está a implementar uma API neste momento.

### <a name="review-and-create"></a>Rever e criar

1. Clique no botão **'Rever + Criar'** para verificar se todos os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação das Aplicações Web Estáticas Azure e providenciar uma Ação GitHub para implementação.

1. Espere que a Ação GitHub esteja completa.

1. Na janela _Geral_ do portal Azure do recurso Azure Static Web Apps, clique na ligação _URL_ para abrir a sua aplicação implementada.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Aplicação implantada":::

#### <a name="custom-hugo-version"></a>Versão hugo personalizada

Quando gera uma Aplicação Web Estática, é gerado um [ficheiro de fluxo de trabalho](./github-actions-workflow.md) que contém as definições de configuração de publicação para a aplicação. Pode designar uma versão Hugo específica no ficheiro workflow, fornecendo um valor na `HUGO_VERSION` `env` secção. A configuração de exemplo a seguir demonstra como definir o Hugo para uma versão específica.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
