---
title: 'Quickstart: Criar uma aplicação web Node.js'
description: Implemente o seu primeiro Node.js Hello World para o Serviço de Aplicações Azure em minutos.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-nodejs-uiex
ms.openlocfilehash: 6c6f0543dcfbecd16ba4176272f928ffd0eb54de
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735119"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar uma aplicação Web Node.js no Azure

::: zone pivot="platform-windows"  

Começa com o Azure App Service criando uma aplicação Node.js/Express localmente usando o Código do Estúdio Visual e, em seguida, implementando a app para a nuvem. Como utiliza um nível de Serviço de Aplicações gratuito, não tem custos para completar este arranque rápido.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)
- <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
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

    ![Iniciar sessão no Azure](media/quickstart-nodejs/sign-in.png)

1. No explorador de **SERVIÇO DE APLICAÇÃO AZURE** do Código VS, selecione o ícone de seta azul para cima para implementar a sua aplicação para o Azure. (Também pode invocar o mesmo comando a partir da Paleta de **Comando** **(Ctrl** + **Shift** + **P)** digitando 'implementar para a web app' e escolhendo o Serviço de **Aplicações Azure: Implementar para a Web App).**

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Screenshot do serviço Azure App em VS Code mostrando o ícone de seta azul selecionado.":::
        
1. Escolha a pasta *nodejs-docs-hello-world.*

1. Escolha uma opção de criação baseada no sistema operativo para o qual pretende implementar:

    - Linux: Escolha **Criar nova Aplicação Web**
    - Windows: Escolha **Criar nova Aplicação Web... Avançado**

1. Digite um nome globalmente único para a sua aplicação web e prima **Enter**. O nome deve ser único em toda a Azure e usar apenas caracteres alfanuméricos ('A-Z', 'a-z', e '0-9') e hífenes ('-').

1. Se apontar para o Linux, selecione uma versão Node.js quando solicitado. Recomenda-se uma versão **LTS.**

1. Se direcionar o Windows, siga as indicações adicionais:
    1. Selecione **Criar um novo grupo de recursos** e, em seguida, insira um nome para o grupo de recursos, como `AppServiceQS-rg` .
    1. Selecione **Windows** para o sistema operativo.
    1. **Selecione Crie um novo plano de Serviço de Aplicações** e, em seguida, insira um nome para o plano (como), `AppServiceQS-plan` em seguida, selecione **F1 Grátis** para o nível de preços.
    1. Escolha **Skip por agora** quando solicitado sobre Insights de Aplicação.
    1. Escolha uma região perto de si ou perto de recursos a que deseje aceder.

1. Depois de responder a todas as solicitações, o CÓDIGO VS mostra os recursos Azure que estão a ser criados para a sua aplicação no pop-up de notificação.

    Ao implementar para o Linux, selecione **Sim** quando solicitado para atualizar a sua configuração para executar `npm install` no servidor Linux alvo.

    ![Solicitação para atualizar a configuração no servidor Linux alvo](media/quickstart-nodejs/server-build.png)

1. Selecione **Sim** quando solicitado com **Sempre implemente o espaço de trabalho "nodejs-docs-hello-world" para (nome da aplicação)"**. Selecionar **Sim** diz ao Código VS para direcionar automaticamente a mesma App Service Web App com implementações subsequentes.

1. Se implementar para o Linux, **selecione Browse Website** na solicitação para ver a sua aplicação web recentemente implantada uma vez concluída a implementação. O navegador deve exibir "Hello World!"

1. Se implementar para o Windows, deve primeiro definir o número de versão Node.js para a aplicação web:

    1. No Código VS, expanda o nó para o novo serviço de aplicações, clique com o botão direito Definições de **aplicação** e selecione **Adicionar Nova Definição...**

        ![Adicionar comando de definição de aplicativos](media/quickstart-nodejs/add-setting.png)

    1. Introduza `WEBSITE_NODE_DEFAULT_VERSION` para a tecla de definição.
    1. Introduza `10.15.2` o valor de definição.
    1. Clique no nó para o serviço de aplicações e selecione **Restart**

        ![Reiniciar o comando de serviço de aplicações](media/quickstart-nodejs/restart.png)

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

    ![Iniciar registos de streaming](media/quickstart-nodejs/view-logs.png)

1. Quando solicitado, opte por ativar a registo e reiniciar a aplicação. Uma vez reiniciada a aplicação, a janela de saída do Código VS abre-se com uma ligação ao fluxo de registo. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Screenshot do pedido do Código do Estúdio Visual para ativar o registo e reiniciar a aplicação com o botão Sim selecionado.":::

1. Após alguns segundos, a janela de saída mostra uma mensagem indicando que está ligado ao serviço de streaming de registos. Pode gerar mais atividade de saída refrescando a página no navegador.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Passos seguintes

Parabéns, conseguiste completar esta rapidinha!

> [!div class="nextstepaction"]
> [Tutorial: Node.js app com o MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Configurar Node.js app](configure-language-nodejs.md)

Confira as outras extensões do Azure.

* [BD do Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha-os todos instalando o pacote de extensões [Node para Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma conta Azure, [inscreva-se hoje](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) para uma conta gratuita com 200 dólares em créditos Azure para experimentar qualquer combinação de serviços.

Você precisa do [Código do Estúdio Visual](https://code.visualstudio.com/) instalado juntamente comNode.js e [ npm](https://nodejs.org/en/download), o gestor de pacotes Node.js.

Também terá de instalar a extensão do [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), que pode utilizar para criar, gerir e implementar aplicações Web Linux na Plataforma Azure como um serviço (PaaS).

### <a name="sign-in"></a>Iniciar sessão

Assim que a extensão estiver instalada, inicie sessão na sua conta Azure. Na Barra de Atividades, selecione o logótipo Azure para mostrar o explorador **do Serviço app AZURE.** Selecione **Iniciar súm em Azure...** e siga as instruções.

![assinar para Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Resolução de problemas

Se vir o erro **"Não é possível encontrar subscrição com nome [ID de subscrição]"**, pode ser porque está por trás de um representante e incapaz de chegar à API Azure. Configure `HTTP_PROXY` `HTTPS_PROXY` variáveis e ambientais com a sua informação de procuração no seu terminal utilizando `export` .

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se a definição das variáveis ambientais não corrigir o problema, contacte-nos selecionando o **I deparar-se com um** botão de problema abaixo.

### <a name="prerequisite-check"></a>Verificação pré-requisito

Antes de continuar, certifique-se de que tem todos os pré-requisitos instalados e configurados.

No Código VS, deverá ver o seu endereço de e-mail Azure na Barra de Estado e a sua subscrição no explorador do **Serviço de APLICAÇÕES AZURE.**

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Crie a sua aplicação Node.js

Em seguida, crie uma aplicação Node.js que pode ser implantada na Cloud. Este quickstart utiliza um gerador de aplicação para eliminar rapidamente a aplicação a partir de um terminal.

> [!TIP]
> Se já completou o [ tutorial deNode.js,](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)pode antecipar-se ao [Deploy to Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Andaime uma nova aplicação com o Gerador Expresso

[O Expresso](https://www.expressjs.com) é um quadro popular para a construção e execução de aplicações Node.js. Pode andaime (criar) uma nova aplicação Express utilizando a ferramenta [Express Generator.](https://expressjs.com/en/starter/generator.html) O Gerador Expresso é enviado como um módulo npm e pode ser executado diretamente (sem instalação) utilizando a ferramenta npm command-line `npx` .

```bash
npx express-generator myExpressApp --view pug --git
```

Os `--view pug --git` parâmetros dizem ao gerador para usar o motor do modelo [pug](https://pugjs.org/api/getting-started.html) (anteriormente conhecido `jade` como) e para criar um `.gitignore` ficheiro.

Para instalar todas as dependências da aplicação, vá à nova pasta e corra `npm install` .

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Executar a aplicação

Em seguida, certifique-se de que a aplicação é executado. A partir do terminal, inicie a aplicação utilizando o `npm start` comando para iniciar o servidor.

```bash
npm start
```

Agora, abra o seu navegador e navegue `http://localhost:3000` para, onde você deve ver algo assim:

![Aplicação Express executando](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Implementar no Azure

Nesta secção, implementa a sua aplicação Node.js utilizando o Código VS e a extensão do Serviço de Aplicações Azure. Este quickstart utiliza o modelo de implementação mais básico onde a sua aplicação é fechada e implementada para uma App Azure Web no Linux.

### <a name="deploy-using-azure-app-service"></a>Implementar usando o Serviço de Aplicações Azure

Primeiro, abra a sua pasta de aplicação no Código VS.

```bash
code .
```

No explorador **AZURE APP SERVICE,** selecione o ícone de seta azul para cima para implementar a sua aplicação para Azure.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Screenshot do menu Azure App Service em Visual Studio Code com a seta de implantação azul selecionada.":::

> [!TIP]
> Também pode ser implantado a partir da Paleta de **Comando** (CTRL + SHIFT + P) digitando 'implementar para a web app' e executando o **Serviço de Aplicações Azure: Implementar para** o comando da Web App.

1. Escolha o diretório que tem atualmente aberto, `myExpressApp` .

1. Escolha **Criar uma nova Aplicação Web,** que implementa o Serviço de Aplicações no Linux por padrão.

1. Digite um nome globalmente único para a sua Web App e prima ENTER. Os caracteres válidos para um nome de aplicação são 'a-z', '0-9' e '--'.

1. Escolha a sua **versãoNode.js,** recomenda-se o LTS.

    O canal de notificação mostra os recursos Azure que estão a ser criados para a sua aplicação.

1. Selecione **Sim** quando solicitado para atualizar a sua configuração para executar `npm install` no servidor alvo. A sua aplicação é então implementada.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Screenshot da solicitação para atualizar a sua configuração no servidor alvo com o botão sim selecionado.":::

1. Quando a implementação começar, é-lhe solicitado que atualize o seu espaço de trabalho para que as implementações posteriores direcionem automaticamente a mesma App Service Web App. Escolha **Sim** para garantir que as suas alterações são implementadas na aplicação correta.

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Screenshot do pedido para atualizar o seu espaço de trabalho com o botão sim selecionado.":::

> [!TIP]
> Certifique-se de que a sua aplicação está a ouvir na porta fornecida pela variável ambiente PORT: `process.env.PORT` .

### <a name="browse-the-app-in-azure"></a>Navegue na app em Azure

Assim que a implementação estiver concluída, **selecione Browse Website** no pedido para ver a sua aplicação web recentemente implementada.

### <a name="troubleshooting"></a>Resolução de problemas

Se vir o erro **"Não tem permissão para ver este diretório ou página."** Dirija-se à secção seguinte e veja a saída de registo para encontrar e corrigir o erro. Se não conseguir consertá-lo, contacte-nos selecionando o **que encontrei num** botão de problema abaixo. Estamos felizes em ajudar!

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Atualizar a aplicação

Pode implementar alterações nesta aplicação utilizando o mesmo processo e escolhendo a aplicação existente em vez de criar uma nova.

## <a name="viewing-logs"></a>Visualização de registos

Nesta secção, aprende-se a visualizar (ou "cauda") os registos da aplicação de Serviço de Aplicações em execução. Quaisquer chamadas `console.log` na aplicação são apresentadas na janela de saída no Código do Estúdio Visual.

Encontre a aplicação no explorador do **Serviço de APLICAÇÕES AZURE,** clique com o botão direito na aplicação e escolha **Ver Registos de Streaming.**

A janela de saída do código VS abre-se com uma ligação ao fluxo de registo.

![Ver Registos de Streaming](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Screenshot do pedido do Código VS para ativar a gravação de ficheiros e reiniciar a aplicação web, com o botão sim selecionado.":::

Após alguns segundos, verá uma mensagem indicando que está ligado ao serviço de streaming de registos. Refresque a página algumas vezes para ver mais atividade.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Passos seguintes

Parabéns, conseguiste completar esta rapidinha!

Em seguida, confira as outras extensões do Azure.

* [BD do Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha-os todos instalando o pacote de extensões [Node para Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)


::: zone-end
