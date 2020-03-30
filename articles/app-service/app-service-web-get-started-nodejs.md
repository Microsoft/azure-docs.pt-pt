---
title: 'Quickstart: Criar uma aplicação web Node.js'
description: Desloque o seu primeiro Node.js Hello World para o Serviço de Aplicações Azure em minutos. Implementa-se usando o Visual Studio Code, que é uma das muitas formas de implementar para o App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: bcb0ae2457175e7511c3539187d72d48e80baa5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047120"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar uma aplicação Web Node.js no Azure 

Inicie-se com o Azure App Service criando uma aplicação Node.js/Express localmente utilizando o Visual Studio Code e, em seguida, implementando a app para a nuvem. Uma vez que utiliza um nível gratuito de Serviço de Aplicações, não incorre em custos para completar este arranque rápido.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)
- [Node.js e npm.](https://nodejs.org) Execute `node --version` o comando para verificar se o Node.js está instalado.
- [Código de estúdio visual.](https://code.visualstudio.com/)
- A extensão do Serviço de [Aplicações Azure](vscode:extension/ms-azuretools.vscode-azureappservice) para Código de Estúdio Visual.

## <a name="clone-and-run-a-local-nodejs-application"></a>Clone e executar uma aplicação local Node.js

1. No seu computador local, abra um terminal e clone o repositório da amostra:

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
    
1. Abra o seu [http://localhost:1337](http://localhost:1337)navegador e navegue para . O navegador deve exibir "Hello World!".

1. Pressione **ctrl**+**C** no terminal para parar o servidor.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Implementar a aplicação no Azure

Nesta secção, implementa a sua aplicação Node.js para o Azure utilizando o VS Code e a extensão do Serviço de Aplicações Azure.

1. No terminal, certifique-se de que está na pasta *nodejs-docs-hello-world,* em seguida, inicie o Visual Studio Code com o seguinte comando:

    ```bash
    code .
    ```

1. Na barra de atividade do Código VS, selecione o logótipo Azure para mostrar o explorador de **SERVIÇO SAC AZURE.** Selecione **iniciar sessão no Azure...** e siga as instruções. (Consulte o sessão de [acesso do Azure](#troubleshooting-azure-sign-in) abaixo se tiver erros.) Uma vez inscrito, o explorador deve mostrar o nome da sua assinatura Azure.

    ![Iniciar sessão no Azure](containers/media/quickstart-nodejs/sign-in.png)

1. No explorador de SERVIÇO SV DO **AZURE APP,** selecione o ícone de seta azul para implantar a sua aplicação para o Azure. (Também pode invocar o mesmo comando da Paleta de **Comando** **(Ctrl**+**Shift**+**P**) digitando 'deploy to web app' e escolhendo **o Azure App Service: Deploy to Web App**).

    ![Implemente para web app](containers/media/quickstart-nodejs/deploy.png)
        
1. Escolha a pasta *nodejs-docs-hello-world.*

1. Escolha uma opção de criação baseada no sistema operativo ao qual pretende implementar:

    - Linux: Escolha **criar nova aplicação web**
    - Windows: Escolha **criar nova aplicação web... Avançado**

1. Digite um nome globalmente único para a sua aplicação web e prima **Enter**. O nome deve ser único em todo o Azure e utilizar apenas caracteres alfanuméricos ('A-Z', 'a-z' e '0-9') e hífenes ('-').

1. Se tiver como alvo o Linux, selecione uma versão Node.js quando for solicitada. Recomenda-se uma versão **LTS.**

1. Se direcionar o Windows, siga as indicações adicionais:
    1. Selecione **Criar um novo grupo de recursos**e, `AppServiceQS-rg`em seguida, insira um nome para o grupo de recursos, como .
    1. Selecione **Windows** para o sistema operativo.
    1. Selecione Criar um novo plano de Serviço de `AppServiceQS-plan` **Aplicações**e, em seguida, insira um nome para o plano (como), em seguida, selecione **F1 Free** para o nível de preços.
    1. Escolha **Skip por enquanto** quando solicitado sobre insights de aplicação.
    1. Escolha uma região perto de si ou perto de recursos a que deseja aceder.

1. Depois de responder a todas as solicitações, o VS Code mostra os recursos Azure que estão a ser criados para a sua aplicação no popup de notificação.

    Ao ser implementado para **Yes** o Linux, selecione `npm install` Sim quando for solicitado para atualizar a sua configuração para ser executado no servidor de destino Linux.

    ![Pronta para atualizar a configuração no servidor target Linux](containers/media/quickstart-nodejs/server-build.png)

1. Selecione **Sim** quando solicitado com Sempre implemente o espaço de **trabalho "nodejs-docs-hello-world" para (nome da aplicação)"**. Selecionar **Sim** diz ao Código VS para direcionar automaticamente a mesma Aplicação Web do Serviço de Aplicações com implementações subsequentes.

1. Se implementar para o Linux, **selecione Browse Website** no pedido para visualizar a sua aplicação web recentemente implementada uma vez que a implementação esteja completa. O navegador deve exibir "Hello World!"

1. Se for implementado para o Windows, tem primeiro de definir o número da versão Node.js para a aplicação web:

    1. No Código VS, expanda o nó para o novo serviço de aplicações, clique direito definições de **aplicação,** e selecione **Adicionar Nova Definição...**

        ![Adicionar comando de definição de aplicativo](containers/media/quickstart-nodejs/add-setting.png)

    1. Introduza `WEBSITE_NODE_DEFAULT_VERSION` a tecla de definição.
    1. Introduza `10.15.2` para o valor de definição.
    1. Clique no nó direito para o serviço de aplicações e selecione **Restart**

        ![Reiniciar o comando de serviço de aplicações](containers/media/quickstart-nodejs/restart.png)

    1. Clique no nó direito para o serviço de aplicações mais uma vez e **selecione Site da Navegação**.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Resolução de problemas de entrada de Azure

Se vir o erro **"Não consigo encontrar subscrição com nome [ID de subscrição]"** ao assinar no Azure, pode ser porque está por trás de um proxy e incapaz de chegar à API Azure. Configure `HTTP_PROXY` `HTTPS_PROXY` e configure variáveis ambientais `export`com a informação de procuração no seu terminal utilizando .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se a definição das variáveis ambientais não corrigir o problema, contacte-nos selecionando o **i encontrei um** botão de emissão acima.

### <a name="update-the-app"></a>Atualizar a aplicação

Pode implementar alterações nesta aplicação fazendo edites no Código VS, guardando os seus ficheiros e, em seguida, usando o mesmo processo que antes de escolher apenas a app existente em vez de criar uma nova.

## <a name="viewing-logs"></a>Registos de visualização

Pode visualizar a saída `console.log`de registo (chamadas para ) a partir da aplicação diretamente na janela de saída do Código VS.

1. No explorador de **SERVIÇO SAC AZURE,** clique no nó da aplicação e escolha **Iniciar registos de streaming**.

    ![Iniciar registos de streaming](containers/media/quickstart-nodejs/view-logs.png)

1. Quando solicitado, opte por ativar a exploração e reiniciar a aplicação. Uma vez reiniciada a aplicação, a janela de saída do Código VS abre-se com uma ligação ao fluxo de registo. 

    ![Ativar a exploração madeireira e reiniciar](containers/media/quickstart-nodejs/enable-restart.png)

1. Após alguns segundos, a janela de saída mostra uma mensagem indicando que está ligado ao serviço de streaming de log. Pode gerar mais atividade de saída refrescando a página no navegador.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Passos seguintes

Parabéns, conseguiste ter conseguido este arranque rápido!

Em seguida, confira as outras extensões Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha-os todos instalando o Pacote de Nó para o pacote de extensão [Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)
