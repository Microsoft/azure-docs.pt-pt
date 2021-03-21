---
title: 'Tutorial: Publicar um site jekyll para Azure Static Web Apps'
description: Saiba como implementar uma aplicação Jekyll para aplicações Web Estáticas Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: 8c6764ad5b63aa2fde07326ab986404ea4312316
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99585181"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Tutorial: Publicar um site jekyll para a Azure Static Web Apps Preview

Este artigo demonstra como criar e implementar uma aplicação web [Jekyll](https://jekyllrb.com/) para [Azure Azure Static Web Apps](overview.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar um website da Jekyll
> - Configurar uma Azure Static Web Apps
> - Implementar a app Jekyll para Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instalar [Jekyll](https://jekyllrb.com/docs/installation/)
  - Pode utilizar o Subsistema Windows para Linux e seguir as instruções do Ubuntu, se necessário.
- Uma conta Azure com uma subscrição ativa. Se não tiver uma, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/)
- Uma conta do GitHub. Se não tiver uma, pode [criar uma conta gratuitamente.](https://github.com/join)

## <a name="create-jekyll-app"></a>Criar a Jekyll App

Criar uma aplicação Jekyll utilizando a Interface da Linha de Comando Jekyll (CLI):

1. A partir do terminal, executar o Jekyll CLI para criar uma nova app.

   ```bash
   jekyll new static-app
   ```

1. Navegue para a aplicação recém-criada.

   ```bash
   cd static-app
   ```

1. Inicialize um novo repositório de Git.

   ```bash
    git init
   ```

1. Cometa as mudanças.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Empurre a sua aplicação para o GitHub

A azure Static Web Apps usa GitHub para publicar o seu website. Os passos seguintes mostram-lhe como criar um repositório GitHub.

1. Crie um repo GitHub em branco (não crie um README) a partir do [https://github.com/new](https://github.com/new) nome **jekyll-azure-estático**.

1. Adicione o repositório GitHub como um controlo remoto ao seu repo local. Certifique-se de adicionar o seu nome de utilizador GitHub no lugar do `<YOUR_USER_NAME>` espaço reservado no comando seguinte.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Empurre o seu repo local até GitHub.

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > O seu ramo de git pode ser nomeado de forma diferente de `main` . Substitua `main` neste comando o valor correto.

## <a name="deploy-your-web-app"></a>Implemente a sua aplicação web

Os passos seguintes mostram-lhe como criar uma nova aplicação estática do site e implantá-la para um ambiente de produção.

### <a name="create-the-application"></a>Criar a aplicação

1. Navegue até ao [portal Azure.](https://portal.azure.com)

1. Clique **em Criar um recurso.**

1. Procure por **Aplicações Web Estáticas.**

1. Clique **em Aplicativos Web Estáticos (Pré-visualização)**.

1. Clique em **Criar**.

1. Para **Subscrição**, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. No _grupo de recursos_, selecione **New**. Em _Novo nome de grupo de recursos,_ insira a **aplicação jekyll-estática** e selecione **OK**.

1. Em seguida, forneça um nome para a sua aplicação na caixa _Nome._ Os caracteres válidos `a-z` `A-Z` incluem, e `0-9` `-` .

1. Para _a Região_, selecione uma região disponível perto de si.

1. Para _SKU_, selecione **Grátis**.

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="Detalhes preenchidos":::

1. Clique **no botão Iniciar sin com GitHub.**

1. Selecione a **Organização** sob a qual criou o repo.

1. Selecione a **aplicação jekyll-estática** como _repositório_.

1. Para o _Ramo_ selecione **principal**.

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="Informação completa do GitHub":::

### <a name="build"></a>Compilar

Em seguida, adiciona definições de configuração que o processo de construção utiliza para construir a sua aplicação. As seguintes definições configuram o ficheiro de fluxo de trabalho gitHub Action.

1. Clique no **Seguinte: Construa >** botão para editar a configuração de construção.

1. Definir _a localização da aplicação_ para **/_site**.

1. Deixe a _localização_ do artefacto da App em branco.

   Um valor para _a localização_ da API não é necessário, uma vez que não está a implementar uma API neste momento.

### <a name="review-and-create"></a>Rever e criar

1. Clique no botão **'Rever + Criar'** para verificar se todos os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação das Aplicações Web Estáticas Azure e providenciar uma Ação GitHub para implementação.

1. A implementação falhará primeiro, porque o ficheiro de fluxo de trabalho precisa de algumas definições específicas da Jekyll. Para adicionar estas definições, navegue no seu terminal e puxe o compromisso com a Ação GitHub para a sua máquina.

   ```bash
   git pull
   ```

1. Abra a aplicação Jekyll num editor de texto e abra o ficheiro _.github/workflows/azure-pages-<WORKFLOW_NAME>.yml._

1. Antes da linha `- name: Build And Deploy` adicionar o seguinte bloco de configuração.

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1.59.1
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. Comprometa o fluxo de trabalho atualizado e empurre para o GitHub.

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. Espere que a Ação GitHub esteja completa.

1. Na janela _'Visão Geral'_ do portal Azure, clique no link _URL_ para abrir a aplicação implementada.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Aplicação implantada":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
