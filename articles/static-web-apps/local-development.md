---
title: Configurar o desenvolvimento local para aplicações web estáticas Azure
description: Aprenda a definir-lhe o seu ambiente de desenvolvimento local para aplicações web estáticas Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 4d6dae8a4f4ed83af3103e95e711bacdb62cf522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326172"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Configurar o desenvolvimento local para pré-visualização de aplicações web estáticas Azure

Uma instância Azure Static Web Apps é composta por dois tipos diferentes de aplicações. A primeira é uma aplicação web para o seu conteúdo estático. As aplicações web são muitas vezes criadas com estruturas frontais e bibliotecas ou com geradores de sites estáticos. O segundo aspeto é a API, que é uma app Azure Functions que proporciona um rico ambiente de desenvolvimento back-end.

Ao correr na nuvem, a Azure Static Web Apps mapeia perfeitamente os pedidos para a `api` rota da aplicação web para a aplicação Azure Functions sem necessitar de configuração CORS. Localmente, precisa configurar a sua aplicação para imitar este comportamento.

Este artigo demonstra as melhores práticas recomendadas para o desenvolvimento local, incluindo os seguintes conceitos:

- Configurar a aplicação web para conteúdo estático
- Configurar a app Azure Functions para a API da sua aplicação
- Depurar e executar a aplicação
- Boas práticas para o arquivo e estrutura de pastas da sua aplicação

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio Code](https://code.visualstudio.com/)
- [Extensão de funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código do Estúdio Visual
- [Extensão do Servidor Ao Vivo](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) para Código do Estúdio Visual
  - Necessário apenas se não estiver a utilizar uma estrutura javaScript frontal ou o CLI do gerador estático do gerador de sítios

## <a name="run-projects-locally"></a>Executar projetos localmente

Executar uma App Web Estática Azure envolve localmente três processos, dependendo se o seu projeto contém ou não uma API.

- Executando um servidor web local
- Funcionando a API
- Ligação do projeto web à API

Dependendo da forma como um site é construído, um servidor web local pode ou não ser obrigado a executar a aplicação no navegador. Ao utilizar quadros javaScript frontais e geradores de locais estáticos, esta funcionalidade é incorporada nos respetivos CLIs (Interfaces de Linha de Comando). As seguintes ligações apontam para a referência do IPC para uma seleção de quadros, bibliotecas e geradores.

### <a name="javascript-frameworks-and-libraries"></a>Estruturas e bibliotecas JavaScript

- [CLI do Angular](https://angular.io/cli)
- [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html)
- [Reagir CLI](https://create-react-app.dev/)

### <a name="static-site-generators"></a>Geradores estáticos

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

Se estiver a utilizar uma ferramenta CLI para servir o seu site, pode saltar para a secção ['Executar a API'.](#run-api-locally)

### <a name="running-a-local-web-server-with-live-server"></a>Executar um servidor web local com Live Server

A extensão live server para Visual Studio Code fornece um servidor web de desenvolvimento local que serve conteúdo estático.

#### <a name="create-a-repository"></a>Criar um repositório

1. Certifique-se de que está conectado ao GitHub e, navegue [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) para e crie um novo projeto GitHub chamado **vanilla-api,** usando este modelo.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="Nova janela de repo do GitHub":::

1. Abra o Visual Studio Code.

1. Prima **F1** para abrir a Paleta de Comandos.

1. Digite **o clone** na caixa de pesquisa e selecione **Git: Clone**.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="opção de clone git no Código do Estúdio Visual":::

1. Introduza o seguinte valor para **URL repositório**.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. Selecione uma localização de pasta para o novo projeto.

1. Quando lhe for pedido para abrir o repositório clonado, clique em **Open** (Abrir).

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="Abra em nova janela":::

Visual Studio Code abre o projeto clonado no editor.

### <a name="run-the-website-locally-with-live-server"></a>Executar o site localmente com Live Server

1. Prima **F1** para abrir a Paleta de Comandos.

1. Digite **servidor ao vivo** na caixa de pesquisa e selecione Live **Server: Abra com servidor ao vivo**

    Um separador de navegador abre para exibir a aplicação.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="Site estático simples em execução no navegador":::

    Esta aplicação faz um pedido HTTP para o `api/message` ponto final. Neste momento, esse pedido está a falhar porque a parte da API desta aplicação precisa de ser iniciada.

### <a name="run-api-locally"></a>Executar API localmente

As APIs de Aplicações Web Estáticas Azure são alimentadas por Funções Azure. Consulte [adicionar uma API a Azure Static Web Apps com Funções Azure](add-api.md) para obter detalhes sobre a adição de uma API a um projeto de Aplicações Web Estáticas Azure.

Como parte do processo de criação da API, é criada uma configuração de lançamento para o Código do Estúdio Visual. Esta configuração está localizada na pasta _.vscode._ Esta pasta contém todas as configurações necessárias para construir e executar a API localmente.

1. No Código do Estúdio Visual, prima **F5** para iniciar a API.

1. Uma nova instância terminal abre mostrando a saída do processo de construção da API.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="API em execução no terminal visual Studio Code":::

   A barra de estado no Código do Estúdio Visual é agora laranja. Esta cor indica que a API está agora em funcionamento e o depurante está ligado.

1. Em seguida, pressione **Ctrl/Cmd** e clique no URL no terminal para abrir uma janela do navegador que chama a API.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="Resultado do ecrã do navegador da chamada API":::

### <a name="debugging-the-api"></a>Depurar a API

1. Abra o ficheiro _api/GetMessage/index.js_ no Código do Estúdio Visual.

1. Clique na margem esquerda na linha 2 para definir um ponto de rutura. Aparece um ponto vermelho que indica que o ponto de rutura está definido.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Breakpoint no Código do Estúdio Visual":::

1. No navegador, atualize a página em execução em <http://127.0.0.1:7071/api/message> .

1. O ponto de rutura é atingido no Código do Estúdio Visual e a execução do programa é interrompida.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Breakpoint hit em Visual Studio Code":::

   Uma [experiência completa de depuragem está disponível no Código do Estúdio Visual](https://code.visualstudio.com/Docs/editor/debugging) para a sua API.

1. Pressione o botão **Continuar** na barra de depuração para continuar a execução.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Continue o botão no Código do Estúdio Visual":::

### <a name="calling-the-api-from-the-application"></a>Chamando a API da aplicação

Quando implementado, a Azure Static Web Apps mapeia automaticamente estes pedidos para os pontos finais na pasta _api._ Este mapeamento garante que os pedidos da aplicação à API parecem ser o exemplo seguinte.

```javascript
let response = await fetch("/api/message");
```

Dependendo se a sua aplicação foi ou não construída com um CLI-quadro JavaScript, existem duas formas de configurar o caminho para a `api` rota ao executar a sua aplicação localmente.

- Ficheiros de configuração do ambiente (recomendados para estruturas e bibliotecas JavaScript)
- Procuração local

### <a name="environment-configuration-files"></a>Ficheiros de configuração do ambiente

Se estiver a construir a sua aplicação com estruturas frontais que tenham um CLI, deverá utilizar ficheiros de configuração ambiental. Cada estrutura ou biblioteca tem uma forma diferente de lidar com estes ficheiros de configuração ambiental. É comum ter um ficheiro de configuração para desenvolvimento que é usado quando a sua aplicação está a funcionar localmente, e um para produção que é usado quando a sua aplicação está em execução em produção. O CLI para a estrutura JavaScript ou o gerador de site estático que está a usar saberá automaticamente utilizar o ficheiro de desenvolvimento localmente e o ficheiro de produção quando a sua aplicação for construída por Azure Static Web Apps.

No ficheiro de configuração de desenvolvimento, pode especificar o caminho para a API, que aponta para a localização local de `http:127.0.0.1:7071` onde a API para o seu site está a funcionar localmente.

```
API=http:127.0.0.1:7071/api
```

No ficheiro de configuração de produção, especifique o caminho para a API como `api` . Desta forma, a sua aplicação chamará a api através de "yoursite.com/api" quando estiver em execução em produção.

```
API=api
```

Estes valores de configuração podem ser referenciados como variáveis de ambiente de nó no JavaScript da aplicação web.

```js
let response = await fetch(`${process.env.API}/message`);
```

Quando o CLI é utilizado para executar o seu site em modo de desenvolvimento ou para construir o site para produção, o `process.env.API` valor é substituído pelo valor do ficheiro de configuração apropriado.

Para obter mais informações sobre a configuração de ficheiros ambientais para quadros e bibliotecas javaScript frontais, consulte estes artigos:

- [Variáveis ambientais angulares](https://angular.io/guide/build#configuring-application-environments)
- [Reagir - Adicionar variáveis de ambiente personalizado](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue - Modos e Variáveis ambientais](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Reiniciar servidor ao vivo

1. Prima **F1** para abrir a Paleta de Comando no Código do Estúdio Visual.

1. Digite **servidor ao vivo** e selecione Live **Server: Stop Live Server**.

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Parar o comando do Servidor Ao Vivo na paleta de comando do Estúdio Visual":::

1. Prima **F1** para abrir a Paleta de Comandos.

1. Digite **servidor ao vivo** e selecione Live **Server: Abra com servidor ao vivo**.

1. Refresque a aplicação em execução em `http://locahost:3000` . O navegador apresenta agora a mensagem devolvida da API.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="Olá da API exibido no navegador":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar as definições da aplicação](application-settings.md)
