---
title: Adicione uma API a Azure Static Web Apps com Funções Azure
description: Começa com as Aplicações Web Estáticas Azure adicionando uma API sem servidor à tua aplicação web estática utilizando Funções Azure.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 53be2ee9b6b3ab46f081747aec58b9c99fc54f0d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349961"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Adicione uma API a Azure Static Web Apps Preview com Funções Azure

Pode adicionar APIs sem servidor a Azure Static Web Apps através da integração com as Funções Azure. Este artigo demonstra como adicionar e implementar uma API num site de Aplicações Web Estáticas Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Conta Azure com uma subscrição ativa.
  - Se não tiver uma conta, pode [criar uma de graça.](https://azure.microsoft.com/free)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Extensão de funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código do Estúdio Visual
- [Extensão do Código do Estúdio Visual do Servidor Ao Vivo.](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
- [Node.js](https://nodejs.org/download/) executar a app API localmente

## <a name="create-a-git-repository"></a>Criar um repositório do Git

Os seguintes passos demonstram como criar um novo repositório e clonar os ficheiros para o seu computador.

1. Certifique-se de que está a iniciar sessão no GitHub e, navegue [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate) para criar um novo repositório.
1. Na caixa de _nomes repositório,_ insira **o meu-baunilha-api.**
1. Clique **em Criar repositório a partir do modelo.**

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Criar um novo repositório a partir de baunilha-básico":::

Assim que o seu projeto for criado, copie o URL no seu navegador para o novo repositório. Usa este URL no Código do Estúdio Visual para clonar o repositório git.

1. Prima **F1** para abrir o comando na Paleta de Comando.
1. Cole o URL no _Git: Clone_ prompt e prima **Enter**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Clone um projeto GitHub usando Código de Estúdio Visual":::

    Siga as instruções para selecionar um local de repositório para clonar o projeto.

## <a name="create-the-api"></a>Criar a API

Em seguida, você cria um projeto Azure Functions como API da aplicação. 

1. Dentro do projeto _my-vanilla-api,_ crie uma sub-pasta chamada **api**.
1. Prima **F1** para abrir a Paleta de Comando
1. Funções do Tipo **Azure: Criar novo projeto...**
1. Prima **Enter**
1. Escolha **Procurar**
1. Selecione a pasta **api** como diretório para o seu espaço de trabalho do projeto
1. Escolha **Selecionar**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="A screenshot mostra a pasta A P I e o botão Select.":::

1. Fornecer as seguintes informações nas instruções:

    - _Selecione um idioma_: Escolha **JavaScript**
    - _Selecione um modelo para a primeira função do seu projeto_: Escolha **o gatilho HTTP**
    - _Fornecer um nome de função_: Insira **GetMessage**
    - _Nível de autorização_: Escolha **Anónimo,** que permite a qualquer pessoa ligar para o ponto final da sua função.
        - Para saber mais sobre os níveis de autorização, consulte [as teclas de Autorização](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

O Visual Studio Code gera um projeto Azure Functions com uma função http desencadeada.

A sua aplicação tem agora uma estrutura de projeto semelhante ao seguinte exemplo.

```files
├── api
│   ├── GetMessage
│   │   ├── function.json
│   │   ├── index.js
│   │   └── sample.dat
│   ├── host.json
│   ├── local.settings.json
│   ├── package.json
│   └── proxies.json
├── index.html
├── readme.md
└── styles.css
```

Em seguida, mudará a `GetMessage` função para devolver uma mensagem à parte frontal.

1. Atualize a `GetMessage` função em _api/GetMessage/index.js_ com o seguinte código.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. Atualize a `GetMessage` configuração em `api/GetMessage/function.json` baixo com as seguintes definições.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```

Com as definições acima, o ponto final da API é:

- Desencadeado quando um pedido HTTP é feito para a função
- Disponível para todos os pedidos independentemente do estado de autenticação
- Exposto através da rota _/api/mensagem_

## <a name="run-the-api-locally"></a>Executar a API localmente

O Código do Estúdio Visual integra-se com [as Ferramentas Core do Azure Functions](../azure-functions/functions-run-local.md) para permitir que execute este projeto no seu computador de desenvolvimento local antes de publicar para o Azure.

> [!TIP]
> Certifique-se de que tem todos os recursos listados na secção [de pré-requisitos instalados](#prerequisites) antes de prosseguir.

1. Executar a função pressionando **F5** para iniciar a aplicação Funções.

1. Se as ferramentas principais do Azure Functions ainda não estiverem instaladas, selecione **Instale** na mensagem.

    As Ferramentas Core mostram a saída da aplicação de execução no painel _Terminal._ Como parte da saída, pode ver o ponto final url da sua função desencadeada por HTTP a funcionar localmente.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="A screenshot mostra o separador TERMINAL onde pode ver o U R L.":::

1. Com as Ferramentas Core em funcionamento, navegue para o seguinte URL para verificar se a API está a funcionar corretamente: `http://localhost:7071/api/message` .

   A resposta no navegador deve ser semelhante ao seguinte exemplo:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Alt textScreenshot mostra uma resposta de texto em um navegador.":::

1. **Press Shift + F5** para parar a sessão de depuragem.

### <a name="call-the-api-from-the-application"></a>Ligue para a API a partir da aplicação

Quando implementados para o Azure, os pedidos à API são automaticamente encaminhados para a app Funções para pedidos enviados para a `api` rota. Trabalhando localmente, você tem que configurar as configurações de aplicação para pedidos de procuração para a API local.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>Atualizar ficheiros HTML para aceder à API

1. Em seguida, atualize o conteúdo do ficheiro _index.html_ com o seguinte código para obter o texto da função API e exibi-lo no ecrã:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading content from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

1. Prima **F5** para iniciar o projeto API.

1. Prima **F1** e escolha **Live Server: Abra com o Servidor Ao Vivo**.

    Deve agora ver a mensagem da API na página web.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="A screenshot mostra a mensagem A P I num browser.":::

   > [!NOTE]
   > Pode utilizar outros servidores http ou proxies para servir o `index.html` ficheiro. Aceder ao `index.html` de vai não `file:///` funcionar.

1. **Press Shift + F5** para parar o projeto API.

### <a name="commit-and-push-your-changes-to-github"></a>Comprometa e empurre as suas alterações para o GitHub

Utilizando o Código do Estúdio Visual, comprometa e empurre as suas alterações para o repositório remoto de git.

1. Prima **F1** para abrir a Paleta de Comando
1. Tipo **Git: Cometer tudo**
1. Adicione uma mensagem de compromisso e **pressione Insira**
1. Pressione **F1**
1. Digite em **Git: empurre** e **pressione Insira**

## <a name="create-a-static-web-app"></a>Criar uma aplicação Web estática

1. Navegue até ao [portal Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**.
1. Pesquisa por **Aplicação Web Estática**
1. Clique **em Aplicativo Web Estático (pré-visualização)**
1. Clique em **Criar**

Em seguida, adicione as definições específicas da aplicação.

1. Selecione a sua _subscrição Azure_
1. Selecione ou crie um novo _Grupo de Recursos_
1. Nomeie a aplicação **my-vanilla-api.**
1. Selecione _Região_ mais próxima de si
1. Selecione o _SKU_ **gratuito**
1. Clique no botão **Iniciar sessão com o GitHub** e autentique-se no GitHub
1. Selecione a sua _Organização_ Preferida
1. Selecione **my-vanilla-api** do _repositório_ drop-down
1. Selecione **mestre** do _ramo_ drop-down
1. Selecione a estrutura da sua escolha para a configuração de construção a partir do drop-down _Build Presets_

 > Estes campos refletirão a estrutura de projeto padrão do tipo de aplicação. Altere os valores de acordo com a sua aplicação.

Em seguida, adicione os seguintes detalhes de construção.

1. **/** Insira para a localização da _App._
1. Introduza **a Api** na caixa _de localização da Api._
1. Limpe o valor predefinido da localização do artefacto da _App,_ deixando a caixa vazia.
1. Clique em **Rever + criar**.
1. Clique no botão **Criar**.

    Uma vez que clica no botão _Criar,_ o Azure faz duas coisas. Em primeiro lugar, os serviços de nuvem subjacentes são criados para suportar a app. Em seguida, um processo de fundo começa a construir e implementar a aplicação.

1. Clique no botão **'Ir a Recursos'** para o levar à página _de Visão Geral_ da aplicação web.

    Como a aplicação está a ser construída em segundo plano, pode clicar no banner que contém um link para ver o estado de construção.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="Fluxo de trabalho do GitHub":::

1. Uma vez concluída a implementação, ou pode navegar para a aplicação web, clicando no link _URL_ mostrado na página _'Vista Geral'._

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Acesso url de aplicativo estático a partir do portal Azure":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se não quiser manter esta aplicação para posterior utilização, pode utilizar os seguintes passos para eliminar a App Web Estática Azure e os seus recursos relacionados.

1. Navegue até ao [portal Azure](https://portal.azure.com)
1. Na barra de pesquisa superior, tipo **grupos de recursos**
1. Clique **em grupos de recursos**
1. Selecione **myResourceGroup**
1. Na página _myResourceGroup,_ certifique-se de que os recursos listados são os que pretende eliminar.
1. Selecione **Eliminar**
1. Digite **myResourceGroup** na caixa de texto
1. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Configurar as definições da aplicação](./application-settings.md)
