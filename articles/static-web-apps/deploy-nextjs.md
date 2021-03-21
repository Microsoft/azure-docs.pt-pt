---
title: 'Tutorial: Implementar sites de Next.js estáticos em Azure Static Web Apps'
description: Gere e implemente Next.js sites dinâmicos com aplicações web estáticas Azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: a22d06137c3ec17851280605ac85c94ef8b342cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563080"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps-preview"></a>Implementar sites de Next.js estáticos na pré-visualização de aplicações web estáticas do Azure

Neste tutorial, aprende-se a implementar um website estático [ geradoNext.js](https://nextjs.org) para a [Azure Static Web Apps](overview.md). Para começar, aprende-se a configurar, configurar e implementar uma aplicação Next.js. Durante este processo, você também aprende a lidar com desafios comuns muitas vezes enfrentados ao gerar páginas estáticas com Next.js

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)
- Uma conta do GitHub. [Crie uma conta gratuita.](https://github.com/join)
- [Node.js](https://nodejs.org) instalados.

## <a name="set-up-a-nextjs-app"></a>Crie uma aplicação Next.js

Em vez de usar o CLI Next.js para criar uma aplicação, pode usar um repositório de iniciação que inclui uma aplicação Next.js existente. Este repositório apresenta uma aplicação Next.js com rotas dinâmicas, que destaca um problema de implementação comum. As rotas dinâmicas precisam de uma configuração de implementação extra sobre a qual aprenderá mais num momento.

Para começar, crie um novo repositório sob a sua conta GitHub a partir de um repositório de modelos.

1. Navegar para [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate)
1. Nomeie o repositório **nextjs-starter**
1. Em seguida, clone o novo repo para a sua máquina. Certifique-se de substituir `<YOUR_GITHUB_ACCOUNT_NAME>` pelo nome da sua conta.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Navegue para a nova aplicação de Next.js clonada:

   ```bash
   cd nextjs-starter
   ```

1. Instalar dependências:

    ```bash
    npm install
    ```

1. Iniciar Next.js app em desenvolvimento:

    ```bash
    npm run dev
    ```

Navegue `http://localhost:3000` para abrir a app, onde deverá ver o seguinte site aberto no seu navegador preferido:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Início Next.js app":::

Ao clicar numa estrutura/biblioteca, deverá ver uma página de detalhes sobre o item selecionado:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Página Detalhes":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Gere um site estático a partir de Next.js construir

Quando constrói um site Next.js `npm run build` utilizando, a aplicação é construída como uma aplicação web tradicional, não como um site estático. Para gerar um site estático, utilize a seguinte configuração de aplicação.

1. Para configurar rotas estáticas, crie ficheiros _nomeadosnext.config.js_ na raiz do seu projeto e adicione o seguinte código..

    ```javascript
    module.exports = {
      trailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```
    
      Esta configuração `/` mapeia para a página Next.js que é servida para a `/` rota, e que é o ficheiro _de página de páginas/index.js._

1. Atualize o _package.jsno_ script de construção da construção para também gerar um site estático após a construção, utilizando o `next export` comando. O `export` comando gera um local estático.

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    Agora, com este comando no lugar, as Aplicações Web Estáticas executarão o `build` script sempre que empurrar um compromisso.

1. Gerar um site estático:

    ```bash
    npm run build
    ```

    O local estático é gerado e copiado numa pasta _na_ raiz do seu diretório de trabalho.

    > [!NOTE]
    > Esta pasta está listada no ficheiro _.gitignore_ porque deve ser gerada por CI/CD quando implementar.

## <a name="push-your-static-website-to-github"></a>Empurre o seu website estático para o GitHub

A Azure Static Web Apps implementa a sua aplicação a partir de um repositório GitHub e continua a fazê-lo para cada compromisso empurrado para um ramo designado. Utilize os seguintes comandos sincronizar as suas alterações no GitHub.

1. Encenar todos os ficheiros alterados

    ```bash
    git add .
    ```

1. Cometer todas as alterações

    ```bash
    git commit -m "Update build config"
    ```

1. Empurre as suas alterações para o GitHub.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Implemente o seu site estático

Os passos a seguir mostram como ligar a app que acabou de empurrar para GitHub para Azure Static Web Apps. Uma vez em Azure, pode implementar a aplicação num ambiente de produção.

### <a name="create-a-static-app"></a>Criar uma aplicação estática

1. Navegue até ao [portal Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**.
1. Procure **Aplicações Web Estáticas**
1. Clique em **Aplicações Web Estáticas (Pré-visualização)**
1. Clique em **Criar**

1. Selecione uma subscrição da lista de entrega de *assinatura* ou utilize o valor predefinido.
1. Clique no **novo** link abaixo do drop-down do *grupo de recursos.* No *nome do grupo de novos recursos,* **escreva o mystaticsite** e clique **em OK**
1. Forneça um nome globalmente único para a sua aplicação na caixa de texto **Name.** Os caracteres válidos `a-z` `A-Z` incluem, , e `0-9` `-` . Este valor é usado como prefixo URL para a sua aplicação estática no formato de `https://<APP_NAME>.azurestaticapps.net` .
1. Na *Região,* escolha uma região mais próxima de si.
1. Selecione **Free** a partir do drop-down SKU.

   :::image type="content" source="media/deploy-nextjs/create-static-web-app.png" alt-text="Criar Aplicação Web Estática":::

### <a name="add-a-github-repository"></a>Adicione um repositório GitHub

A nova conta Static Web Apps precisa de acesso ao repositório com a sua aplicação Next.js para que possa implementar automaticamente compromissos.

1. Clique **no iniciar sômência com o botão GitHub**
1. Selecione a **Organização** sob a qual criou o repo para o seu projeto Next.js, que pode ser o seu nome de utilizador GitHub.
1. Encontre e selecione o nome do repositório que criou anteriormente.
1. Escolha **principal** como ramo da queda do *Ramo.*

   :::image type="content" source="media/deploy-nextjs/connect-github.png" alt-text="Ligar o GitHub":::

### <a name="configure-the-build-process"></a>Configure o processo de construção

A azure Static Web Apps é construído para realizar automaticamente tarefas comuns como instalar módulos npm e executar `npm run build` durante cada implementação. Existem, no entanto, algumas definições como a pasta de origem da aplicação e a pasta de destino de construção que precisa de configurar manualmente.

1. Clique no separador **'Construir'** para configurar a pasta de saída estática.

   :::image type="content" source="media/deploy-nextjs/build-tab.png" alt-text="Construir separador":::

2. Digite na caixa *de texto de localização do artefacto* da App. 

### <a name="review-and-create"></a>Rever e criar

1. Clique no botão **'Rever + Criar'** para verificar se todos os detalhes estão corretos.
1. Clique em **Criar** para iniciar a criação do recurso e também providenciar uma Ação GitHub para implementação.
1. Uma vez concluída a implementação, clique em **Ir para o recurso**
1. Na janela _'Vista Geral',_ clique no link *URL* para abrir a aplicação implementada. 

Se o site notar imediatamente carregar, então o fluxo de trabalho de fundo GitHub Actions ainda está em execução. Uma vez concluído o fluxo de trabalho, pode clicar em atualizar o navegador para ver a sua aplicação web.
Se o site notar imediatamente carregar, então o fluxo de trabalho de fundo GitHub Actions ainda está em execução. Uma vez concluído o fluxo de trabalho, pode clicar em atualizar o navegador para ver a sua aplicação web.

Pode verificar o estado dos fluxos de trabalho das Ações navegando nas Ações para o seu repositório:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>Alterações de sincronização

Quando criou a aplicação, a Azure Static Web Apps criou um ficheiro de fluxo de trabalho GitHub Actions no seu repositório. Tens de levar este ficheiro para o teu repositório local para que o teu histórico seja sincronizado.

Volte ao terminal e executar o seguinte comando `git pull origin main` .

## <a name="configure-dynamic-routes"></a>Configure rotas dinâmicas

Navegue para o site recém-implantado e clique num dos logótipos da estrutura ou da biblioteca. Em vez de obter uma página de detalhes, obtém uma página de erro 404.

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="404 em rotas dinâmicas":::

A razão deste erro deve-se Next.js apenas gerou a página inicial com base na configuração da aplicação.

## <a name="generate-static-pages-from-dynamic-routes"></a>Gerar páginas estáticas a partir de rotas dinâmicas

1. Atualize o ficheiro _next.config.js_ para que Next.js utilize uma lista de todos os dados disponíveis para gerar páginas estáticas para cada quadro/biblioteca:

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     trailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > A `exportPathMap` função é uma função assínca, para que possa fazer um pedido a uma API nesta função e utilizar a lista devolvida para gerar os caminhos.

2. Empurre as novas alterações para o seu repositório GitHub e aguarde alguns minutos enquanto o GitHub Actions volta a construir o seu site. Após a construção estar concluída, o erro 404 desaparece.

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="404 em rotas dinâmicas fixas":::

> [!div class="nextstepaction"]
> [Configurar um domínio personalizado](custom-domain.md)
