---
title: 'Tutorial: Implemente websites Next.js renderizados por servidor estoque em Aplicações Web Estáticas Do Azure'
description: Gere e implemente sites dinâmicos Next.js com Aplicações Web Estáticas Azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.openlocfilehash: fe139921cb73ee0e224c995e2dd5eb5fc50f3979
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599854"
---
# <a name="deploy-server-rendered-nextjs-websites-on-azure-static-web-apps-preview"></a>Implemente websites Next.js renderizados por servidor estoque na pré-visualização de aplicações web da Web do Azure

Neste tutorial, você aprende a implementar um site estática [da Next.js](https://nextjs.org) gerado para [Aplicações Web Estáticas Azure](overview.md). Para começar, aprende-se a configurar, configurar e implementar uma aplicação Next.js. Durante este processo, você também aprende a lidar com desafios comuns muitas vezes enfrentados ao gerar páginas estáticas com Next.js

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/)
- Uma conta do GitHub. [Crie uma conta gratuitamente.](https://github.com/join)
- Ter o [Node.js](https://nodejs.org) instalado.

## <a name="set-up-a-nextjs-app"></a>Configurar uma aplicação Next.js

Em vez de utilizar o Next.js CLI para criar uma aplicação, pode utilizar um repositório inicial que inclui uma aplicação Next.js existente. Este repositório apresenta uma app Next.js com rotas dinâmicas, que destaca um problema comum de implementação. As rotas dinâmicas precisam de uma configuração de implantação extra sobre a qual aprenderá mais dentro de momento.

Para começar, crie um novo repositório sob a sua conta GitHub a partir de um repositório de modelos. 

1. Navegar para<http://github.com/staticwebdev/nextjs-starter/generate>
1. Nomeie o repositório **nextjs-starter**
1. Em seguida, clone o novo repo para a sua máquina. Certifique-se de substituir `<YOUR_GITHUB_ACCOUNT_NAME>` pelo nome da sua conta.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Navegue para a nova aplicação Next.js:

   ```bash
   cd nextjs-starter
   ```

1. Instale dependências:

    ```bash
    npm install
    ```

1. Inicie a aplicação Next.js em desenvolvimento:

    ```bash
    npm run dev
    ```

Navegue <http://localhost:3000> para abrir a aplicação, onde deverá ver o seguinte site aberto no seu navegador preferido:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Iniciar a aplicação Next.js":::

Quando clicar numa estrutura/biblioteca, deve ver uma página de detalhes sobre o item selecionado:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Página Detalhes":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Gerar um site estático a partir da construção next.js

Quando se constrói um site Next.js utilizando `npm run build` , a aplicação é construída como uma aplicação web tradicional, não como um site estático. Para gerar um local estático, utilize a seguinte configuração de aplicação.

1. Para configurar rotas estáticas, crie ficheiros _nomeados next.config.js_ na raiz do seu projeto e adicione o seguinte código..

    ```javascript
    module.exports = {
      exportTrailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```
    
      Esta configuração mapeia `/` para a página Next.js que é servida para a rota, e que é o arquivo de `/` página _páginas/index.js._

1. Atualize o script de construção do _pacote.json_para também gerar um local estático após a construção, usando o `next export` comando. O `export` comando gera um local estático.

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    Agora, com este comando no lugar, as Aplicações Web Estáticas executarão o `build` script sempre que empurrar um compromisso.

1. Gerar um local estático:

    ```bash
    npm run build
    ```

    O site estático é gerado e copiado para uma pasta _na_ raiz do seu diretório de trabalho.

    > [!NOTE]
    > Esta pasta está listada no ficheiro _.gitignore_ porque deve ser gerada por CI/CD quando se implanta.

## <a name="push-your-static-website-to-github"></a>Empurre o seu site estático para o GitHub

As Aplicações Web Estáticas Azure implementam a sua aplicação a partir de um repositório GitHub e continua a fazê-lo por cada compromisso empurrado para um ramo designado. Utilize os seguintes comandos sincronizar as suas alterações ao GitHub.

1. Palco todos os ficheiros alterados

    ```bash
    git add .
    ```

1. Cometer todas as alterações

    ```bash
    git commit -m "Update build config"
    ```

1. Empurre as suas alterações para o GitHub.

    ```bash
    git push origin master
    ```

## <a name="deploy-your-static-website"></a>Implemente o seu site estático

Os seguintes passos mostram como ligar a aplicação que acaba de empurrar para o GitHub para as Aplicações Web Estáticas do Azure. Uma vez em Azure, pode implementar a aplicação para um ambiente de produção.

### <a name="create-a-static-app"></a>Criar uma aplicação estática

1. Navegue para o [portal do Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**
1. Pesquisa de **aplicações web estáticas**
1. Clique em **aplicações web estáticas (pré-visualização)**
1. Clique em **Criar**

1. Selecione uma subscrição da lista de abandono de *subscrição* ou utilize o valor predefinido.
1. Clique no **novo** link abaixo do grupo *Derecursos.* Em *novo nome de grupo de recursos,* escreva site **mystaticsite** e clique EM **OK**
1. Forneça um nome globalmente único para a sua aplicação na caixa de texto **Name.** Os caracteres válidos `a-z` `A-Z` incluem, e `0-9` `-` . Este valor é usado como prefixo URL para a sua aplicação estática no formato de `https://<APP_NAME>.azurestaticapps.net` .
1. Na *Região,* escolha uma região mais próxima de si.
1. Selecione **Free** a partir da entrega do SKU.

   :::image type="content" source="media/deploy-nextjs/create-static-web-app.png" alt-text="Criar aplicação web estática":::

### <a name="add-a-github-repository"></a>Adicione um repositório GitHub

A nova conta De Aplicações Web Estáticas precisa de ter acesso ao repositório com a sua app Next.js para que possa implementar automaticamente os compromissos.

1. Clique no **'Iniciar sessão' com o botão GitHub**
1. Selecione a **Organização** sob a qual criou o repo para o seu projeto Next.js, que pode ser o seu nome de utilizador GitHub.
1. Encontre e selecione o nome do repositório que criou anteriormente.
1. Escolha o **mestre** como o ramo da queda do *Ramo.*

   :::image type="content" source="media/deploy-nextjs/connect-github.png" alt-text="Ligar GitHub":::

### <a name="configure-the-build-process"></a>Configure o processo de construção

As Aplicações Web Estáticas Azure são construídas para executar automaticamente tarefas comuns como instalar módulos npm e correr `npm run build` durante cada implementação. Existem, no entanto, algumas definições como a pasta fonte de aplicação e a pasta de destino de construção que precisa configurar manualmente.

1. Clique no separador **'Construir'** para configurar a pasta de saída estática.

   :::image type="content" source="media/deploy-nextjs/build-tab.png" alt-text="Construir separador":::

2. **Digite** na caixa de texto de localização do artefacto da *App.*

### <a name="review-and-create"></a>Rever e criar

1. Clique no botão **Rever + Criar** para verificar se os detalhes estão corretos.
1. Clique em **Criar** para iniciar a criação do recurso e também fornecer uma Ação GitHub para implementação.
1. Uma vez concluída a implementação, clique **em ir para o recurso**
1. Na janela _Overview,_ clique no link *URL* para abrir a sua aplicação implantada. 

Se o site notar imediatamente a carga, então o fluxo de trabalho de fundo GitHub Actions ainda está em execução. Uma vez concluído o fluxo de trabalho, pode clicar em atualizar o navegador para visualizar a sua aplicação web.
Se o site notar imediatamente a carga, então o fluxo de trabalho de fundo GitHub Actions ainda está em execução. Uma vez concluído o fluxo de trabalho, pode clicar em atualizar o navegador para visualizar a sua aplicação web.

Pode verificar o estado dos fluxos de trabalho das Ações navegando para as Ações para o seu repositório:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>Alterações de sincronização

Quando criou a aplicação, as Web Apps Azure Static criaram um ficheiro de fluxo de trabalho GitHub Actions no seu repositório. Tens de levar este ficheiro ao teu repositório local para que o teu historial de tis seja sincronizado.

Volte ao terminal e execute o seguinte comando `git pull origin master` .

## <a name="configure-dynamic-routes"></a>Configurar rotas dinâmicas

Navegue para o site recém-implantado e clique num dos logótipos da estrutura ou biblioteca. Em vez de obter uma página de detalhes, obtém-se uma página de erro 404.

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="404 em rotas dinâmicas":::

A razão para este erro é porque next.js apenas gerou a página inicial com base na configuração da aplicação.

## <a name="generate-static-pages-from-dynamic-routes"></a>Gerar páginas estáticas de rotas dinâmicas

1. Atualize o ficheiro _next.config.js_ de modo a que next.js utilize uma lista de todos os dados disponíveis para gerar páginas estáticas para cada quadro/biblioteca:

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     exportTrailingSlash: true,
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
   > A `exportPathMap` função é uma função de assinos, para que possa fazer um pedido a uma API nesta função e utilizar a lista devolvida para gerar os caminhos.

2. Empurre as novas alterações para o seu repositório GitHub e aguarde alguns minutos enquanto a GitHub Actions constrói novamente o seu site. Após a construção estar completa, o erro 404 desaparece.

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="404 em rotas dinâmicas fixas":::

> [!div class="nextstepaction"]
> [Configurar um domínio personalizado](custom-domain.md)
