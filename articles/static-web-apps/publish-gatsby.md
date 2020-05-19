---
title: 'Tutorial: Publique um site de Gatsby para Web Apps Estáticas Azure'
description: Este tutorial mostra-lhe como implementar uma aplicação De Gatsby para Aplicações Web Estáticas Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 14706b623c6973c09b499e4fcd905e3eed430898
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599620"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Tutorial: Publique um site de Gatsby para a pré-visualização de aplicações da Web Estática do Azure

Este artigo demonstra como criar e implementar uma aplicação web [da Gatsby](https://gatsbyjs.org) para [aplicações web estáticas do Azure.](overview.md) O resultado final é um novo site de Aplicações Web Estáticas (com as ações gitHub associadas) que lhe dão controlo sobre como a aplicação é construída e publicada.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> - Criar uma aplicação Gatsby
> - Configurar um site de web apps estáticos Azure
> - Implementar a app Gatsby para O Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Se não tiver uma, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/)
- Uma conta do GitHub. Se não tiver uma, pode [criar uma conta gratuitamente.](https://github.com/join)
- Ter o [Node.js](https://nodejs.org) instalado.

## <a name="create-a-gatsby-app"></a>Criar uma App Gatsby

Criar uma aplicação Gatsby utilizando a Interface de Linha de Comando de Gatsby (CLI):

1. Abrir um terminal
1. Utilize a ferramenta [npx](https://www.npmjs.com/package/npx) para criar uma nova aplicação com o ClI Gatsby. Esta operação poderá demorar alguns minutos.

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

Você precisa ter um repositório no GitHub para criar um novo recurso Azure Static Web Apps.

1. Crie um repositório GitHub em branco (não crie uma README) a partir da [https://github.com/new](https://github.com/new) chamada **gatsby-static-web-app**.

1. Em seguida, adicione o repositório GitHub que acabou de criar como um controlo remoto para o seu repo local. Certifique-se de adicionar o seu nome de utilizador GitHub no lugar do `<YOUR_USER_NAME>` espaço reservado no seguinte comando.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. Empurre o seu repositório local até ao GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Implemente a sua aplicação web

Os seguintes passos mostram-lhe como criar uma nova aplicação de site estático e implantá-la para um ambiente de produção.

### <a name="create-the-application"></a>Criar a aplicação

1. Navegue para o [portal do Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**
1. Pesquisa de **aplicações web estáticas**
1. Clique em **aplicações web estáticas (pré-visualização)**
1. Clique em **Criar**

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Criar uma Web Apps Estática (Pré-visualização) no portal":::

1. Para _Subscrição_, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. No _grupo Recursos,_ selecione **New**. Em novo nome de _grupo de recursos,_ introduza **a aplicação web-gatsby-estática e** selecione **OK**.

1. Em seguida, forneça um nome globalmente único para a sua aplicação na caixa **Nome.** Os caracteres válidos `a-z` `A-Z` incluem, e `0-9` `-` . Este valor é usado como prefixo URL para a sua Aplicação Web Estática no formato de `https://<YOUR_APP_NAME>.azurestaticapps.net` .

1. Para _região,_ selecione uma região disponível perto de si.

1. Para _SKU,_ selecione **Free**.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Detalhes preenchidos":::

1. Clique no **'Iniciar sessão' com** o botão GitHub.

1. Selecione a **Organização** sob a qual criou o repositório.

1. Selecione a **aplicação web gatsby-estática** como o _Repositório_ .

1. Para o _Branch_ selecione **master**.

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Informação completa do GitHub":::

### <a name="build"></a>Compilação

Em seguida, adicione as definições de configuração que o processo de construção utiliza para construir a sua aplicação.

1. Clique no **seguinte: Construa >** botão para editar a configuração de construção

1. Para configurar as definições do passo em GitHub Actions, detete a localização da _Aplicação_ para **/** .

1. Detete a localização do artefacto da _aplicação_ para **o público**.

   Um valor para a localização da _API_ não é necessário, uma vez que não está a implementar uma API neste momento.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Construir Definições":::

### <a name="review-and-create"></a>Rever e criar

1. Clique no botão **Rever + Criar** para verificar se os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação da App Service Static Web App e fornecer uma Ação GitHub para implementação.

1. Uma vez que a implementação esteja concluída clique, **vá para o recurso**.

1. No ecrã de recursos, clique no link _URL_ para abrir a sua aplicação implantada. Pode ter de esperar um minuto ou dois para que a Ação GitHub esteja concluída.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Aplicação implantada":::

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
