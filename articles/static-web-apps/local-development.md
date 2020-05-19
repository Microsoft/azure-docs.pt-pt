---
title: Configurar o desenvolvimento local para aplicações web estáticas azure
description: Aprenda a definir o seu ambiente de desenvolvimento local para Aplicações Web Estáticas Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 36d580b7659325d4bf5f13889f774ddaa2ab0702
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597129"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Configurar o desenvolvimento local para a pré-visualização de aplicações da Web Estática azure

Uma instância de Web Apps Estática selada é composta por dois tipos diferentes de aplicações. A primeira é uma aplicação web para o seu conteúdo estático. As aplicações web são muitas vezes criadas com estruturas frontais e bibliotecas ou com geradores de site estático. O segundo aspeto é a API, que é uma app Azure Functions que proporciona um ambiente de desenvolvimento rico.

Ao correr na nuvem, as Web Apps Estáticas do Azure mapeiam sem problemas os pedidos para a `api` rota da aplicação web para a aplicação Funções Azure sem necessitar da configuração CORS. Localmente, precisa configurar a sua aplicação para imitar este comportamento.

Este artigo demonstra as melhores práticas recomendadas para o desenvolvimento local, incluindo os seguintes conceitos:

- Configurar a aplicação web para conteúdo estático
- Configurar a aplicação Funções Azure para a API da sua aplicação
- Depuração e execução da aplicação
- Boas práticas para o arquivo da sua aplicação e estrutura de pastas

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio Code](https://code.visualstudio.com/)
- [Extensão de funções azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual
- [Extensão do Servidor Ao Vivo](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) para Código de Estúdio Visual
  - Necessário apenas se não estiver a usar uma estrutura de JavaScript frontal ou o CLI do gerador de site estático

## <a name="run-projects-locally"></a>Executar projetos localmente

Executar uma Web App Estática Azure localmente envolve três processos, dependendo se o seu projeto contém ou não uma API.

- Executar um servidor web local
- Execução da API
- Ligar o projeto web à API

Dependendo da forma como um website é construído, um servidor web local pode ou não ser obrigado a executar a aplicação no navegador. Ao utilizar as estruturas frontais do JavaScript e os geradores estáticos do local, esta funcionalidade é incorporada nos respetivos CLIs (Interfaces de Linha de Comando). As seguintes ligações apontam para a referência cli para uma seleção de quadros, bibliotecas e geradores.

### <a name="javascript-frameworks-and-libraries"></a>Quadros e bibliotecas JavaScript

- [Angular CLI](https://angular.io/cli)
- [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html)
- [Reagir CLI](https://create-react-app.dev/)

### <a name="static-site-generators"></a>Geradores de site estático

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Rio Jekyll](https://jekyllrb.com/docs/usage/)

Se estiver a utilizar uma ferramenta CLI para servir o seu site, pode saltar para a secção [Desativação da API.](#run-api-locally)

### <a name="running-a-local-web-server-with-live-server"></a>Executar um servidor web local com live server

A extensão live server para Visual Studio Code fornece um servidor web de desenvolvimento local que serve conteúdo estático.

#### <a name="create-a-repository"></a>Criar um repositório

1. Navegue [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) e crie um novo projeto GitHub chamado **vanilla-api,** usando este modelo.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="Nova janela de repo gitHub":::

1. Abra o Visual Studio Code.

1. Pressione **f1** para abrir a Paleta de Comando.

1. Digite **o clone** na caixa de pesquisa e selecione **Git: Clone**.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="git opção clone no Código do Estúdio Visual":::

1. Introduza o seguinte valor para **url de repositório**.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. Selecione uma localização de pasta para o novo projeto.

1. Quando for solicitado a abertura do repositório clonado, **selecione Open**.

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="Abra em nova janela":::

Visual Studio Code abre o projeto clonado no editor.

### <a name="run-the-website-locally-with-live-server"></a>Executar o site localmente com Live Server

1. Pressione **f1** para abrir a Paleta de Comando.

1. Digite **o Servidor Ao Vivo** na caixa de pesquisa e selecione Live **Server: Abra com o Live Server**

    Abre-se um separador de navegador para exibir a aplicação.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="Simples site estático correndo no navegador":::

    Este pedido faz um pedido http para o `api/message` ponto final. Neste momento, esse pedido está a falhar porque a parte da API deste pedido precisa de ser iniciada.

### <a name="run-api-locally"></a>Executar API localmente

As APIs de Aplicações Web Estáticas Azure são alimentadas por Funções Azure. Consulte [adicionar uma API a Aplicações Web Estáticas Azure com funções Azure](add-api.md) para mais detalhes sobre a adição de um API a um projeto de Web Apps Estátáticas Azure.

Como parte do processo de criação da API, é criada uma configuração de lançamento para o Código do Estúdio Visual. Esta configuração está localizada na pasta _.vscode._ Esta pasta contém todas as definições necessárias para a construção e execução da API localmente.

1. No Visual Studio Code, prima **F5** para iniciar a API.

1. Abre-se uma nova instância terminal que mostra a saída do processo de construção da API.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="API em execução no terminal de Código de Estúdio Visual":::

   A barra de estado no Código do Estúdio Visual é agora laranja. Esta cor indica que a API está agora a funcionar e o desordeiro está ligado.

1. Em seguida, prima **Ctrl/Cmd** e clique no URL do terminal para abrir uma janela de navegador que chama a API.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="Resultado do display do navegador da chamada API":::

### <a name="debugging-the-api"></a>Depurando a API

1. Abra o ficheiro _api/GetMessage/index.js_ no Código do Estúdio Visual.

1. Clique na margem esquerda na linha 2 para definir um ponto de rutura. Aparece um ponto vermelho que indica que o ponto de rutura está definido.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Breakpoint no Código do Estúdio Visual":::

1. No navegador, refresque a página em <http://127.0.0.1:7071/api/message> execução em .

1. O breakpoint é atingido no Código do Estúdio Visual e a execução do programa é interrompida.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Breakpoint hit no Código do Estúdio Visual":::

   Uma experiência completa [de depuração está disponível no Visual Studio Code](https://code.visualstudio.com/Docs/editor/debugging) para a sua API.

1. Pressione o botão **Continuar** na barra de depuração para continuar a execução.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Continue o botão no Código do Estúdio Visual":::

### <a name="calling-the-api-from-the-application"></a>Chamar a API da aplicação

Quando implementados, as Aplicações Web Estáticas do Azure mapeiam automaticamente estes pedidos para os pontos finais da pasta _API._ Este mapeamento garante que os pedidos da aplicação para a API se pareçam com o seguinte exemplo.

```javascript
let response = await fetch("/api/message");
```

Dependendo se a sua aplicação é ou não construída com uma clI de estrutura JavaScript, existem duas maneiras de configurar o caminho para a rota ao executar a `api` sua aplicação localmente.

- Arquivos de configuração do ambiente (recomendados para quadros e bibliotecas JavaScript)
- Procuração local

### <a name="environment-configuration-files"></a>Arquivos de configuração do ambiente

Se estiver a construir a sua aplicação com estruturas frontais que possuam um CLI, deve utilizar ficheiros de configuração ambiental. Cada quadro ou biblioteca tem uma forma diferente de lidar com estes ficheiros de configuração ambiental. É comum ter um ficheiro de configuração para o desenvolvimento que é usado quando a sua aplicação está a funcionar localmente, e um para produção que é usado quando a sua aplicação está em execução. O CLI para a estrutura JavaScript ou gerador de site estático que está a utilizar saberá automaticamente utilizar o ficheiro de desenvolvimento localmente e o ficheiro de produção quando a sua aplicação for construída por Web Apps Estáticas Azure.

No ficheiro de configuração de desenvolvimento, pode especificar o caminho para a API, que aponta para a localização local de `http:127.0.0.1:7071` onde a API para o seu site está a funcionar localmente.

```
API=http:127.0.0.1:7071/api
```

No ficheiro de configuração de produção, especifique o caminho para a API como `api` . Desta forma, a sua aplicação chamará a api via "yoursite.com/api" ao funcionar em produção.

```
API=api
```

Estes valores de configuração podem ser referenciados como variáveis ambientais do nó no JavaScript da aplicação web.

```js
let response = await fetch(`${process.env.API}/message`);
```

Quando o CLI é utilizado para executar o seu site em modo de desenvolvimento ou para construir o site para produção, o `process.env.API` valor é substituído pelo valor do ficheiro de configuração apropriado.

Para obter mais informações sobre a configuração de ficheiros ambientais para quadros e bibliotecas JavaScript front-end, consulte estes artigos:

- [Variáveis de ambiente angular](https://angular.io/guide/build#configuring-application-environments)
- [Reagir - Adicionar variáveis ambientais personalizadas](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue - Variáveis de Modos e Ambiente](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Reiniciar o Servidor Ao Vivo

1. Pressione **f1** para abrir a Paleta de Comando em Código de Estúdio Visual.

1. Digite **o Servidor Ao Vivo** e selecione Live **Server: Pare**o Servidor Ao Vivo .

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Pare o comando do Servidor Ao Vivo na paleta de comando do Estúdio Visual":::

1. Pressione **f1** para abrir a Paleta de Comando.

1. Digite **o Servidor Ao Vivo** e selecione Live **Server: Abra com o Live Server**.

1. Refresque a aplicação em `http://locahost:3000` execução em . O navegador apresenta agora a mensagem devolvida da API.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="Olá da API exibida no navegador":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar as definições da aplicação](application-settings.md)
