---
title: 'Início rápido: criar um aplicativo Web node. js'
description: Implante seu primeiro Olá, Mundo node. js para Azure App serviço em minutos. Você implanta usando Visual Studio Code, que é uma das várias maneiras de implantar no serviço de aplicativo.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 09/30/2019
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 91494cc9c1e3a1fc159702bdbb7f68a4423b604c
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671367"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar uma aplicação Web Node.js no Azure 

O serviço de Azure App fornece um serviço de hospedagem na Web altamente escalonável e com aplicação de patch automática. Este guia de início rápido mostra como implantar um aplicativo node. js no serviço Azure App.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma conta do Azure, [Inscreva-se hoje](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) para obter uma conta gratuita com $200 em créditos do Azure para experimentar qualquer combinação de serviços.

Você precisa de [Visual Studio Code](https://code.visualstudio.com/) instalado juntamente com [node. js e NPM](https://nodejs.org/en/download), o Gerenciador de pacotes do node. js.

Você também precisará instalar a [extensão de serviço Azure app](vscode:extension/ms-azuretools.vscode-azureappservice), que pode ser usada para criar, gerenciar e implantar aplicativos Web do Linux na plataforma como um serviço do Azure (PaaS).

### <a name="sign-in"></a>Iniciar sessão

Depois que a extensão for instalada, faça logon em sua conta do Azure. Na barra de atividades, selecione no logotipo do Azure para mostrar o Gerenciador do **serviço de aplicativo do Azure** . Selecione **entrar no Azure...** e siga as instruções.

![entrar no Azure](containers/media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Resolução de problemas

Se você vir o erro **"não é possível encontrar a assinatura com o nome [ID da assinatura]"** , pode ser porque você está atrás de um proxy e não consegue acessar a API do Azure. Configure `HTTP_PROXY` e `HTTPS_PROXY` variáveis de ambiente com suas informações de proxy em seu terminal usando `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se a definição das variáveis de ambiente não corrigir o problema, entre em contato conosco selecionando o botão **eu executei um problema** abaixo.

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

Antes de continuar, verifique se você tem todos os pré-requisitos instalados e configurados.

No VS Code, você deve ver seu endereço de email do Azure na barra de status e sua assinatura no Gerenciador de **serviço de aplicativo do Azure** .

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Criar seu aplicativo node. js

Em seguida, crie um aplicativo node. js que pode ser implantado na nuvem. Este guia de início rápido usa um gerador de aplicativos para scaffoldr rapidamente o aplicativo de um terminal.

> [!TIP]
> Se você já tiver concluído o [tutorial do node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), poderá pular para [implantar no Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Scaffold um novo aplicativo com o Express Generator

[Express](https://www.expressjs.com) é uma estrutura popular para compilar e executar aplicativos node. js. Você pode scaffold (criar) um novo aplicativo expresso usando a ferramenta do [gerador Express](https://expressjs.com/en/starter/generator.html) . O gerador Express é fornecido como um módulo NPM e pode ser executado diretamente (sem instalação) usando a ferramenta de linha de comando NPM `npx`.

```bash
npx express-generator myExpressApp --view pug --git
```

Os parâmetros de `--view pug --git` dizem ao gerador para usar o mecanismo de modelo [Pug](https://pugjs.org/api/getting-started.html) (anteriormente conhecido como `jade`) e para criar um arquivo de `.gitignore`.

Para instalar todas as dependências do aplicativo, vá para a nova pasta e execute `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Executar a aplicação

Em seguida, verifique se o aplicativo é executado. No terminal, inicie o aplicativo usando o comando `npm start` para iniciar o servidor.

```bash
npm start
```

Agora, abra o navegador e navegue até [http://localhost:3000](http://localhost:3000), onde você verá algo assim:

![Executando aplicativo expresso](containers/media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Implementar no Azure

Nesta seção, você implantará seu aplicativo node. js usando VS Code e a extensão de serviço Azure App. Este guia de início rápido usa o modelo de implantação mais básico em que seu aplicativo é compactado e implantado em um aplicativo Web do Azure no Linux.

### <a name="deploy-using-azure-app-service"></a>Implantar usando o serviço Azure App

Primeiro, abra a pasta do aplicativo no VS Code.

```bash
code .
```

No Gerenciador do **serviço de aplicativo do Azure** , selecione o ícone de seta para cima azul para implantar seu aplicativo no Azure.

![Implantar no aplicativo Web](containers/media/quickstart-nodejs/deploy.png)

> [!TIP]
> Você também pode implantar a partir da **paleta de comandos** (Ctrl + Shift + P) digitando ' implantar no aplicativo Web ' e executando o comando serviço de **Azure App: implantar no aplicativo Web** .

1. Escolha o diretório que você abriu no momento, `myExpressApp`.

1. Escolha uma opção de criação com base no sistema operacional no qual você deseja implantar:

    - Linux: escolha **criar novo aplicativo Web**.
    - Windows: escolha **criar novo aplicativo Web** e selecione a opção **avançado** .

1. Digite um nome global exclusivo para seu aplicativo Web e pressione ENTER. Os caracteres válidos para um nome de aplicativo são ' a-z ', ' 0-9 ' e '-'.

1. Se estiver direcionando para o Linux, selecione uma versão do node. js quando solicitado. Uma versão do **LTS** é recomendada.

1. Se estiver direcionando o Windows usando a opção **avançado** , siga os prompts adicionais:
    1. Selecione **criar um novo grupo de recursos**e insira um nome para o grupo de recursos.
    1. Selecione **Windows** para o sistema operacional.
    1. Selecione um plano do serviço de aplicativo existente ou crie um novo. Você pode selecionar um tipo de preço ao criar um novo plano.
    1. Escolha **ignorar agora** quando receber uma solicitação sobre Application insights.
    1. Escolha uma região perto de você ou perto dos recursos que deseja acessar.

1. Depois de responder a todos os prompts, o canal de notificação mostra os recursos do Azure que estão sendo criados para seu aplicativo.

1. Selecione **Sim** quando solicitado a atualizar sua configuração para executar `npm install` no servidor de destino. Em seguida, seu aplicativo é implantado.

    ![Implantação configurada](containers/media/quickstart-nodejs/server-build.png)

1. Quando a implantação for iniciada, você será solicitado a atualizar seu espaço de trabalho para que as implantações posteriores sejam automaticamente direcionadas ao mesmo aplicativo Web do serviço de aplicativo. Escolha **Sim** para garantir que suas alterações sejam implantadas no aplicativo correto.

    ![Implantação configurada](containers/media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Certifique-se de que seu aplicativo está escutando na porta fornecida pela variável de ambiente PORT: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Procurar o aplicativo no Azure

Quando a implantação for concluída, selecione **procurar site** no prompt para exibir seu aplicativo Web implantado recentemente.

### <a name="troubleshooting"></a>Resolução de problemas

Se você vir o erro **"você não tem permissão para exibir este diretório ou página."** , o aplicativo provavelmente não foi iniciado corretamente. Vá para a próxima seção e exiba a saída do log para localizar e corrigir o erro. Se você não conseguir corrigi-lo, entre em contato conosco selecionando o botão " **eu executei um problema** " abaixo. Estamos felizes em ajudar!

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Atualizar o aplicativo

Você pode implantar alterações nesse aplicativo usando o mesmo processo e escolhendo o aplicativo existente em vez de criar um novo.

## <a name="viewing-logs"></a>Exibindo logs

Nesta seção, você aprenderá a exibir (ou "Finalize") os logs do aplicativo do serviço de aplicativo em execução. Todas as chamadas para `console.log` no aplicativo são exibidas na janela saída em Visual Studio Code.

Localize o aplicativo no Gerenciador do **serviço de aplicativo do Azure** , clique com o botão direito do mouse no aplicativo e escolha **Exibir logs de streaming**.

Quando solicitado, escolha habilitar registro em log e reiniciar o aplicativo. Depois que o aplicativo for reiniciado, a janela VS Code saída será aberta com uma conexão com o fluxo de log.

![Exibir logs de streaming](containers/media/quickstart-nodejs/view-logs.png)

![Habilitar registro em log e reiniciar](containers/media/quickstart-nodejs/enable-restart.png)

Após alguns segundos, você verá uma mensagem indicando que você está conectado ao serviço de streaming de log. Atualize a página algumas vezes para ver mais atividades.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Passos seguintes

Parabéns, você concluiu com êxito este guia de início rápido!

Em seguida, Confira as outras extensões do Azure.

* [BD do Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas de CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou então, faça tudo isso instalando o [pacote de nós para o pacote de extensão do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .
