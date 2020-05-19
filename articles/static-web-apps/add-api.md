---
title: Adicione uma API a Aplicações Web Estáticas Azure com Funções Azure
description: Inicie-se com aplicações Web estáticas Azure adicionando um API sem servidor à sua aplicação estátática usando funções Azure.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598459"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Adicione uma API à Pré-visualização de aplicações web estáticas azure com funções Azure

Pode adicionar APIs sem servidor estática a Aplicações Web Estáticas Azure através da integração com funções Azure. Este artigo demonstra como adicionar e implementar uma API para um site de Web Apps Estática seletiva seletiva.

Para obter informações sobre como proteger as rotas da API, consulte o guia de [encaminhamento](routes.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Extensão de funções azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual
- [Extensão do Código](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) do Estúdio Visual do Servidor Vivo.

## <a name="create-a-git-repository"></a>Criar um repositório git 

Os seguintes passos demonstram como criar um novo repositório e clonar os ficheiros para o seu computador.

1. Navegue https://github.com/staticwebdev/vanilla-basic/generate para criar um novo repositório.
1. Na caixa de _nome repositório,_ introduza **a minha baunilha-api.**
1. Clique **em criar repositório a partir do modelo**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Criar um novo repositório a partir de baunilha-básico":::

Assim que o seu projeto for criado, pode usar o Código do Estúdio Visual para clonar o repositório Git.

1. Pressione **f1** para abrir o comando na Paleta de Comando.
1. Colhe o URL no _Git: Clone_ prompt, e pressione **Enter**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Clone um projeto GitHub usando código de estúdio visual":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Clone um projeto GitHub usando código de estúdio visual":::


## <a name="create-your-local-project"></a>Crie o seu projeto local

Nesta secção, você usa o Código de Estúdio Visual para criar um projeto local de Funções Azure. Mais tarde, publica a app Funções para o Azure.

1. Dentro do projeto _my-vanilla-api,_ crie uma subpasta chamada **api.**

   > [!NOTE]
   > Pode dar a esta pasta qualquer nome. Este exemplo `api` utiliza. 

2. Pressione **F1** para abrir a Paleta de Comando
3. Funções do tipo **Azure: Criar novo projeto...**
4. Pressione **Entrar**
5. Escolha **Navegar**
6. Selecione a pasta **api** como o diretório para o seu espaço de trabalho do projeto
7. Escolha **Selecionar**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Criar uma nova Função Azure usando o Código de Estúdio Visual":::

8. Forneça as seguintes informações nas instruções:

    - _Selecione um idioma para o seu projeto de função_: Escolha **JavaScript**
    - _Selecione um modelo para a primeira função do seu projeto_: Escolha o **gatilho HTTP**
    - _Fornecer um nome de função_: Type **GetMessage**
    - _Nível de autorização_: Escolha **o Anonymous,** que permite a qualquer pessoa ligar para o seu ponto final de função.
        - Para saber sobre os níveis de autorização, consulte [as teclas de autorização.](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)

9. Utilizando esta informação, o Visual Studio Code gera um projeto de Funções Azure com um gatilho HTTP.
    - Pode ver os ficheiros do projeto local na janela exploradora do Visual Studio Code.
    - Para saber mais sobre os ficheiros que são criados, consulte [ficheiros de projetos gerados](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files).

10. A sua aplicação deverá agora ter uma estrutura de projeto semelhante a este exemplo.

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

11. Em seguida, atualize a `GetMessage` função sob _api/GetMessage/index.js_ com o seguinte código.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Atualize a `GetMessage` configuração `api/GetMessage/function.json` com as seguintes definições.

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

- Desencadeado com um pedido HTTP é feito para a função
- Disponível para todos os pedidos independentemente do estado de autenticação
- Exposto através da rota _/api/mensagem_

## <a name="run-the-function-locally"></a>Executar localmente a função

O Visual Studio Code integra-se com as [Ferramentas Core funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local) para permitir que execute este projeto no seu computador de desenvolvimento local antes de publicar no Azure.

1. Executar a função premindo **F5** para iniciar a aplicação Funções, e a saída de Ferramentas Core é exibida no painel _Terminal._

2. Se as ferramentas core funções do Azure ainda não estiverem instaladas, **selecione Instalar** no momento da pronta.

    Quando as Ferramentas Core são instaladas, a sua aplicação começa no painel _Terminal._ Como parte da saída, pode ver o ponto final url da sua função desencadeada em HTTP a funcionar localmente.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Criar uma nova Função Azure usando o Código de Estúdio Visual":::

3. Com as Ferramentas Core em execução, navegue para o seguinte URL para executar um `GET` pedido.

   <http://localhost:7071/api/message>

   Uma resposta é devolvida, que se parece com a seguinte no navegador:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Criar uma nova Função Azure usando o Código de Estúdio Visual":::

Depois de verificar que a função funciona corretamente, pode ligar para a API da aplicação JavaScript.

### <a name="call-the-api-from-the-application"></a>Ligue para a API da aplicação

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Atualizar ficheiros para aceder à API

1. Em seguida, atualize o conteúdo do ficheiro _index.html_ com o seguinte código para recolher o texto da função API e exibi-lo no ecrã:

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
       <p>Loading message from the API: <b id="name">...</b></p>
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

   Com as Ferramentas Core em execução, utilize a extensão do Código do Estúdio Visual [do Servidor Vivo](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) para servir o ficheiro _index.html_ e abra-o no navegador. 

2. Prima **F1** e escolha **Live Server: Abra com o Live Server**.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Criar uma nova Função Azure usando o Código de Estúdio Visual":::

   > [!NOTE]
   > Pode utilizar outros servidores OU proxies HTTP para servir o `index.html` ficheiro. Aceder ao `index.html` de `file:///` não funcionar.

### <a name="commit-and-push-your-changes-to-github"></a>Comprometa e empurre as suas alterações para o GitHub

Utilizando o Código do Estúdio Visual, comprometa e empurre as suas alterações para o repositório de git remoto.

1. Pressionando **f1** para abrir a Paleta de Comando
1. Tipo **Git: Comprometa todos**
1. Adicione uma mensagem de compromisso
1. Tipo em **Git: empurre**

## <a name="create-a-static-web-app"></a>Criar uma aplicação web estática

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Criar aplicação estática no portal Azure - ecrã 1":::

1. Navegue para o [portal do Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**
1. Pesquisa de **aplicações web estáticas**
1. Clique em **aplicações web estáticas (pré-visualização)**
1. Clique em **Criar**
1. Selecione a sua _subscrição Azure_
1. Selecione ou crie um novo _Grupo de Recursos_
1. Diga o nome da app **my-vanilla-api.**
1. _Região_ selecionada mais próxima de si
1. Selecione o _SKU_ **gratuito**
1. Clique no **Sign-in com** o botão GitHub e autenticar com gitHub
1. Selecione a sua _Organização_ Preferida
1. Selecione **my-vanilla-api** do _Repository_ drop-down
1. Selecione **mestre** da queda do _Branch_
1. Clique no **seguinte: Construa >** botão para editar a configuração de construção

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Criar aplicação estática no portal Azure - ecrã 2":::

Em seguida, adicione os seguintes detalhes da construção.

1. Insira **./** para a localização da _Aplicação_.

1. Introduza **api** na caixa de _localização Api._

   Este é o nome da pasta API criada no passo anterior.
   
1. Limpe o valor predefinido da localização do artefacto da _App,_ deixando a caixa vazia.

1. Clique em **Rever + criar**.

| Definição | Descrição             | Necessário |
| -------- | ----------------------- |
|  Localização da aplicação | A localização do código-fonte da aplicação estática | Sim |
|  Localização Api | A localização do backend da API. Isto aponta para a pasta raiz do projeto Da App funções do Azure | Não |
|  Localização do artefacto da aplicação | A localização da pasta de saída de construção. Algumas estruturas javaScript front-end têm um passo de construção que coloca ficheiros de produção numa pasta. Esta definição aponta para a pasta de saída de construção. | Não |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Criar aplicação estática no portal Azure - ecrã 3":::

1. Clique em **Criar**
1. Aguarde a colocação para terminar (isto pode demorar um minuto)
1. Navegar para`https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. Certifique-se de que a construção é bem sucedida

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="Fluxo de trabalho GitHub":::

A API implantada estará disponível em `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>` .

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="Fluxo de trabalho GitHub":::

Também pode encontrar o URL de aplicação do portal Azure:

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Aceder a URL de aplicação estática do portal Azure":::

Em alternativa, pode aceder diretamente à sua Web App Azure Static `https://<STATIC_APP_NAME>.azurestaticapps.net` e verificar o resultado no navegador.

## <a name="clean-up-resources"></a>Limpar recursos

Se não quiser manter esta aplicação para posterior utilização, pode utilizar os seguintes passos para eliminar a Aplicação Web Estática Azure e os seus recursos relacionados.

1. Navegue para o [portal do Azure](https://portal.azure.com)
1. Na barra de pesquisa superior, digite **grupos de recursos**
1. Clique **em grupos de recursos** 
1. Selecione **myResourceGroup**
1. Na página _myResourceGroup,_ certifique-se de que os recursos listados são os que pretende eliminar.
1. **Selecione Excluir**
1. Digite **myResourceGroup** na caixa de texto
1. Selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar as definições da aplicação](./application-settings.md)
