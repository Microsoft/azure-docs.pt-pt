---
title: 'Tutorial: Implementar sites de Nuxt.js renderizados por servidores em Azure Static Web Apps'
description: Gere e implemente Nuxt.js sites dinâmicos com aplicações web estáticas Azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 8f21f5fa8ee9035fe594cecff37a63b1ef2115cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563471"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>Implementar sites de Nuxt.js renderizados por servidores na pré-visualização de aplicações web estáticas do Azure

Neste tutorial, aprende-se a implementar um website estático [ geradoNuxt.js](https://nuxtjs.org) para a [Azure Static Web Apps](overview.md). Para começar, aprende-se a configurar, configurar e implementar uma aplicação Nuxt.js. Durante este processo, você também aprende a lidar com desafios comuns muitas vezes enfrentados ao gerar páginas estáticas com Nuxt.js

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)
- Uma conta do GitHub. [Crie uma conta gratuita.](https://github.com/join)
- [Node.js](https://nodejs.org) instalados.

## <a name="set-up-a-nuxtjs-app"></a>Crie uma aplicação Nuxt.js

Pode configurar um novo projeto de Nuxt.js utilizando `create-nuxt-app` . Em vez de um novo projeto, neste tutorial começa-se por clonar um repositório existente. Este repositório é criado para demonstrar como implementar uma aplicação dinâmica Nuxt.js como um site estático.

1. Crie um novo repositório sob a sua conta GitHub a partir de um repositório de modelos.
1. Navegar para [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate)
1. Nomeie o repositório **nuxtjs-starter**
1. Em seguida, clone o novo repo para a sua máquina. Certifique-se de que substitui <YOUR_GITHUB_ACCOUNT_NAME> pelo nome da sua conta.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Navegue para a nova aplicação de Nuxt.js clonada:

   ```bash
   cd nuxtjs-starter
   ```

1. Instalar dependências:

    ```bash
    npm install
    ```

1. Iniciar Nuxt.js app em desenvolvimento:

    ```bash
    npm run dev
    ```

Navegue `http://localhost:3000` para abrir a app, onde deverá ver o seguinte site aberto no seu navegador preferido:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Início Nuxt.js app":::

Ao clicar numa estrutura/biblioteca, deverá ver uma página de detalhes sobre o item selecionado:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Página Detalhes":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Gere um site estático a partir de Nuxt.js construir

Quando constrói um site Nuxt.js `npm run build` utilizando, a aplicação é construída como uma aplicação web tradicional, não como um site estático. Para gerar um site estático, utilize a seguinte configuração de aplicação.

1. Atualize o _package.jsno_ script de construção do sucum para gerar apenas um site estático usando o `nuxt generate` comando:

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    Agora, com este comando no lugar, as Aplicações Web Estáticas executarão o `build` script sempre que empurrar um compromisso.

1. Gerar um site estático:

    ```bash
    npm run build
    ```

    Nuxt.js gerará o site estático e copiá-lo-á numa pasta _dist_ na raiz do seu diretório de trabalho.

    > [!NOTE]
    > Esta pasta está listada no ficheiro _.gitignore_ porque deve ser gerada por CI/CD quando implementar.

## <a name="push-your-static-website-to-github"></a>Empurre o seu website estático para o GitHub

A Azure Static Web Apps implementa a sua aplicação a partir de um repositório GitHub e continua a fazê-lo para cada compromisso empurrado para um ramo designado. Utilize os seguintes comandos sincronizar as suas alterações no GitHub.

1. Fase todos os ficheiros alterados:

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

### <a name="create-an-azure-static-web-apps-preview-resource"></a>Criar um recurso de pré-visualização de aplicações web estáticas Azure

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

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="Criar Aplicação Web Estática":::

### <a name="add-a-github-repository"></a>Adicione um repositório GitHub

A nova conta Static Web Apps precisa de acesso ao repositório com a sua aplicação Nuxt.js para que possa implementar automaticamente compromissos.

1. Clique **no iniciar sômência com o botão GitHub**
1. Selecione a **Organização** sob a qual criou o repo para o seu projeto Nuxt.js, que pode ser o seu nome de utilizador GitHub.
1. Encontre e selecione o nome do repositório que criou anteriormente.
1. Escolha **principal** como ramo da queda do *Ramo.*

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="Ligar o GitHub":::

### <a name="configure-the-build-process"></a>Configure o processo de construção

A azure Static Web Apps é construído para realizar automaticamente tarefas comuns como instalar módulos npm e executar `npm run build` durante cada implementação. Existem, no entanto, algumas definições como a pasta de origem da aplicação e a pasta de destino de construção que precisa de configurar manualmente.

1. Clique no separador **'Construir'** para configurar a pasta de saída estática.

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="Construir separador":::

1. Digitar na caixa de *texto de localização do artefacto* da App. 

### <a name="review-and-create"></a>Rever e criar

1. Clique no botão **'Rever + Criar'** para verificar se todos os detalhes estão corretos.
1. Clique em **Criar** para iniciar a criação do recurso e também providenciar uma Ação GitHub para implementação.
1. Uma vez concluída a implementação, clique em **Ir para o recurso**
1. Na janela _'Vista Geral',_ clique no link *URL* para abrir a aplicação implementada. 

Se o site notar imediatamente carregar, então o fluxo de trabalho de fundo GitHub Actions ainda está em execução. Uma vez concluído o fluxo de trabalho, pode clicar em atualizar o navegador para ver a sua aplicação web.

Pode verificar o estado dos fluxos de trabalho das Ações navegando nas Ações para o seu repositório:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Alterações de sincronização

Quando criou a aplicação, a Azure Static Web Apps criou um ficheiro de fluxo de trabalho GitHub Actions no seu repositório. Tens de levar este ficheiro para o teu repositório local para que o teu histórico seja sincronizado.

Volte ao terminal e executar o seguinte comando `git pull origin main` .

## <a name="configure-dynamic-routes"></a>Configure rotas dinâmicas

Navegue para o site recém-implantado e clique num dos logótipos da estrutura ou da biblioteca. Em vez de obter uma página de detalhes, obtém uma página de erro 404.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="404 em rotas dinâmicas":::

A razão para isso é que, Nuxt.js gerou o site estático, só o fez para a página inicial. Nuxt.js pode gerar `.html` ficheiros estáticos equivalentes para cada `.vue` ficheiro de páginas, mas há uma exceção. 

Se a página for uma página dinâmica, por `_id.vue` exemplo, não terá informação suficiente para gerar um HTML estático a partir de uma página tão dinâmica. Terá de fornecer explicitamente os caminhos possíveis para as rotas dinâmicas.

## <a name="generate-static-pages-from-dynamic-routes"></a>Gerar páginas estáticas a partir de rotas dinâmicas

1. Atualize o ficheiro _nuxt.config.js_ para que Nuxt.js utilize uma lista de todos os dados disponíveis para gerar páginas estáticas para cada quadro/biblioteca:

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` é uma função assínca, para que possa fazer um pedido a uma API nesta função e usar a lista devolvida para gerar os caminhos.

2. Empurre as novas alterações para o seu repositório GitHub e aguarde alguns minutos enquanto o GitHub Actions volta a construir o seu site. Após a construção estar concluída, o erro 404 desaparece.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="404 em rotas dinâmicas fixas":::

> [!div class="nextstepaction"]
> [Configurar um domínio personalizado](custom-domain.md)
