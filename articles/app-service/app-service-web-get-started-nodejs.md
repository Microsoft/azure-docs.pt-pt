---
title: 'Quickstart: Criar uma aplicação web Node.js'
description: Implemente o seu primeiro Node.js Hello World para o Serviço de Aplicações Azure em minutos. Implementa-se usando o Código do Estúdio Visual, que é uma das muitas formas de implantar no Serviço de Aplicações.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18, devx-track-javascript
ms.openlocfilehash: 0e72c17ab20d092a710bb21b1ff6d3d6418e452f
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170260"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar uma aplicação Web Node.js no Azure 

Começa com o Azure App Service criando uma aplicação Node.js/Express localmente usando o Código do Estúdio Visual e, em seguida, implementando a app para a nuvem. Como utiliza um nível de Serviço de Aplicações gratuito, não tem custos para completar este arranque rápido.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)
- [Node.js e npm.](https://nodejs.org) Verifique o comando `node --version` para verificar se Node.js está instalada.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Serviço de Aplicações Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) para Código de Estúdio Visual.

## <a name="clone-and-run-a-local-nodejs-application"></a>Clone e executar uma aplicação de Node.js local

1. No seu computador local, abra um terminal e clone o repositório de amostras:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navegue na nova pasta de aplicações:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Inicie a aplicação para testá-la localmente:

    ```bash
    npm start
    ```
    
1. Abra o seu navegador e navegue para `http://localhost:1337` . O navegador deve exibir "Hello World!".

1. Pressione **ctrl** + **C** no terminal para parar o servidor.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Implementar a aplicação no Azure

Nesta secção, implementa a sua aplicação Node.js para o Azure utilizando o Código VS e a extensão do Serviço de Aplicações Azure.

1. No terminal, certifique-se de que está na pasta *nodejs-docs-hello-world* e, em seguida, inicie o Código do Estúdio Visual com o seguinte comando:

    ```bash
    code .
    ```

1. Na barra de atividade do Código VS, selecione o logótipo Azure para mostrar o explorador **do Serviço app AZURE.** Selecione **Iniciar súm em Azure...** e siga as instruções. (Ver [resolução de problemas Azure abaixo](#troubleshooting-azure-sign-in) se encontrar erros.) Uma vez assinado, o explorador deve mostrar o nome da sua subscrição Azure.

    ![Iniciar sessão no Azure](containers/media/quickstart-nodejs/sign-in.png)

1. No explorador de **SERVIÇO DE APLICAÇÃO AZURE** do Código VS, selecione o ícone de seta azul para cima para implementar a sua aplicação para o Azure. (Também pode invocar o mesmo comando a partir da Paleta de **Comando** **(Ctrl** + **Shift** + **P)** digitando 'implementar para a web app' e escolhendo o Serviço de **Aplicações Azure: Implementar para a Web App).**

    ![Implementar para web app](containers/media/quickstart-nodejs/deploy.png)
        
1. Escolha a pasta *nodejs-docs-hello-world.*

1. Escolha uma opção de criação baseada no sistema operativo para o qual pretende implementar:

    - Linux: Escolha **Criar nova Aplicação Web**
    - Windows: Escolha **Criar nova Aplicação Web... Avançado**

1. Digite um nome globalmente único para a sua aplicação web e prima **Enter**. O nome deve ser único em toda a Azure e usar apenas caracteres alfanuméricos ('A-Z', 'a-z', e '0-9') e hífenes ('-').

1. Se apontar para o Linux, selecione uma versão Node.js quando solicitado. Recomenda-se uma versão **LTS.**

1. Se direcionar o Windows, siga as indicações adicionais:
    1. Selecione **Criar um novo grupo de recursos**e, em seguida, insira um nome para o grupo de recursos, como `AppServiceQS-rg` .
    1. Selecione **Windows** para o sistema operativo.
    1. **Selecione Crie um novo plano de Serviço de Aplicações**e, em seguida, insira um nome para o plano (como), `AppServiceQS-plan` em seguida, selecione **F1 Grátis** para o nível de preços.
    1. Escolha **Skip por agora** quando solicitado sobre Insights de Aplicação.
    1. Escolha uma região perto de si ou perto de recursos a que deseje aceder.

1. Depois de responder a todas as solicitações, o CÓDIGO VS mostra os recursos Azure que estão a ser criados para a sua aplicação no pop-up de notificação.

    Ao implementar para o Linux, selecione **Sim** quando solicitado para atualizar a sua configuração para executar `npm install` no servidor Linux alvo.

    ![Solicitação para atualizar a configuração no servidor Linux alvo](containers/media/quickstart-nodejs/server-build.png)

1. Selecione **Sim** quando solicitado com **Sempre implemente o espaço de trabalho "nodejs-docs-hello-world" para (nome da aplicação)"**. Selecionar **Sim** diz ao Código VS para direcionar automaticamente a mesma App Service Web App com implementações subsequentes.

1. Se implementar para o Linux, **selecione Browse Website** na solicitação para ver a sua aplicação web recentemente implantada uma vez concluída a implementação. O navegador deve exibir "Hello World!"

1. Se implementar para o Windows, deve primeiro definir o número de versão Node.js para a aplicação web:

    1. No Código VS, expanda o nó para o novo serviço de aplicações, clique com o botão direito Definições de **aplicação**e selecione **Adicionar Nova Definição...**

        ![Adicionar comando de definição de aplicativos](containers/media/quickstart-nodejs/add-setting.png)

    1. Introduza `WEBSITE_NODE_DEFAULT_VERSION` para a tecla de definição.
    1. Introduza `10.15.2` o valor de definição.
    1. Clique no nó para o serviço de aplicações e selecione **Restart**

        ![Reiniciar o comando de serviço de aplicações](containers/media/quickstart-nodejs/restart.png)

    1. Clique no nó para o serviço de aplicações mais uma vez e selecione **Browse Website**.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Resolução de problemas Azure sign-in

Se vir o erro **"Não é possível encontrar subscrição com nome [ID de subscrição]"** ao iniciar sessão no Azure, pode ser porque está por detrás de um representante e não consegue chegar à API Azure. Configure `HTTP_PROXY` `HTTPS_PROXY` variáveis e ambientais com a sua informação de procuração no seu terminal utilizando `export` .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se a definição das variáveis ambientais não corrigir o problema, contacte-nos selecionando o **I deparar-se com um** botão de problema acima.

### <a name="update-the-app"></a>Atualizar a aplicação

Pode implementar alterações nesta aplicação, fazendo edições no Código VS, guardando os seus ficheiros e, em seguida, usando o mesmo processo que antes apenas de escolher a app existente em vez de criar uma nova.

## <a name="viewing-logs"></a>Visualização de registos

Pode visualizar a saída de registo (chamadas `console.log` para) a partir da aplicação diretamente na janela de saída do Código VS.

1. No explorador do **Serviço de APLICAÇÕES AZURE,** clique com o nó da aplicação e escolha **Iniciar Registos de Streaming.**

    ![Iniciar registos de streaming](containers/media/quickstart-nodejs/view-logs.png)

1. Quando solicitado, opte por ativar a registo e reiniciar a aplicação. Uma vez reiniciada a aplicação, a janela de saída do Código VS abre-se com uma ligação ao fluxo de registo. 

    ![Ativar o registo e o reinício](containers/media/quickstart-nodejs/enable-restart.png)

1. Após alguns segundos, a janela de saída mostra uma mensagem indicando que está ligado ao serviço de streaming de registos. Pode gerar mais atividade de saída refrescando a página no navegador.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Próximos passos

Parabéns, conseguiste completar esta rapidinha!

> [!div class="nextstepaction"]
> [Tutorial: Node.js app com o MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Configurar Node.js app](configure-language-nodejs.md)

Confira as outras extensões do Azure.

* [BD do Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha-os todos instalando o pacote de extensões [Node para Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)

