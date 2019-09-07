---
title: Criar um aplicativo Web node. js-serviço de Azure App
description: Como implantar um aplicativo node. js no serviço Azure App
author: msangapu
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: 8d679a95cc89c2ae7774b7f7b51b9d0aadd89d12
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390964"
---
# <a name="create-a-nodejs-app-in-azure"></a>Criar um aplicativo node. js no Azure

O serviço de Azure App fornece um serviço de hospedagem na Web altamente escalonável e com aplicação de patch automática. Este guia de início rápido mostra como implantar um aplicativo node. js no serviço Azure App.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma conta do Azure, [Inscreva-se hoje](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) para obter uma conta gratuita com $200 em créditos do Azure para experimentar qualquer combinação de serviços.

Você precisa de [Visual Studio Code](https://code.visualstudio.com/) instalado juntamente com [node. js e NPM](https://nodejs.org/en/download), o Gerenciador de pacotes do node. js.

Você também precisará instalar a [extensão de serviço Azure app](vscode:extension/ms-azuretools.vscode-azureappservice), que pode ser usada para criar, gerenciar e implantar aplicativos Web do Linux na plataforma como um serviço do Azure (PaaS).

### <a name="sign-in"></a>Iniciar sessão

Depois que a extensão for instalada, faça logon em sua conta do Azure. Na barra de atividades, clique no logotipo do Azure para mostrar o Gerenciador do **serviço de aplicativo do Azure** . Clique em **entrar no Azure...** e siga as instruções.

![entrar no Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Resolução de problemas

Se você vir o erro **"não é possível encontrar a assinatura com o nome [ID da assinatura]"** , pode ser porque você está atrás de um proxy e não consegue acessar a API do Azure. Configure `HTTP_PROXY` `export`e `HTTPS_PROXY` variáveis de ambiente com suas informações de proxy no seu terminal usando o.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se a definição das variáveis de ambiente não corrigir o problema, entre em contato conosco clicando no botão **eu executei um problema** abaixo.

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

Antes de continuar, verifique se você tem todos os pré-requisitos instalados e configurados.

No VS Code, você deve ver seu endereço de email do Azure na barra de status e sua assinatura no Gerenciador de **serviço de aplicativo do Azure** .

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Criar seu aplicativo node. js

Em seguida, crie um aplicativo node. js que pode ser implantado na nuvem. Este guia de início rápido usa um gerador de aplicativos para scaffoldr rapidamente o aplicativo de um terminal.

> [!TIP]
> Se você já tiver concluído o [tutorial do node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), poderá pular para [implantar o site](#deploy-the-website).

### <a name="install-the-express-generator"></a>Instalar o gerador Express

[Express](https://www.expressjs.com) é uma estrutura popular para compilar e executar aplicativos node. js. Você pode scaffold (criar) um novo aplicativo expresso usando a ferramenta do [gerador Express](https://expressjs.com/en/starter/generator.html) . O gerador Express é fornecido como um módulo NPM e instalado usando a ferramenta `npm`de linha de comando NPM.

```bash
npm install -g express-generator
```

O `-g` comutador instala o Express Generator globalmente em seu computador para que você possa executá-lo de qualquer lugar.

### <a name="scaffold-a-new-application"></a>Scaffold um novo aplicativo

Em seguida, Scaffold um novo aplicativo expresso `myExpressApp` chamado executando:

```bash
express myExpressApp --view pug --git
```

Os `--view pug --git` parâmetros dizem ao gerador para usar o mecanismo de modelo [Pug](https://pugjs.org/api/getting-started.html) (anteriormente conhecido `jade`como) e para criar `.gitignore` um arquivo.

Para instalar todas as dependências do aplicativo, vá para a nova pasta e execute `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Executar a aplicação

Em seguida, verifique se o aplicativo é executado. No terminal, inicie o aplicativo usando o `npm start` comando para iniciar o servidor.

```bash
npm start
```

Agora, abra o navegador e navegue até [http://localhost:3000](http://localhost:3000), onde você verá algo assim:

![Executando aplicativo expresso](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>Implementar o site

Nesta seção, você implantará seu site do node. js usando VS Code e a extensão de serviço Azure App. Este guia de início rápido usa o modelo de implantação mais básico em que seu aplicativo é compactado e implantado em um aplicativo Web do Azure no Linux.

### <a name="deploy-using-azure-app-service"></a>Implantar usando o serviço Azure App

Primeiro, abra a pasta do aplicativo no VS Code.

```bash
code .
```

No Gerenciador do **serviço de aplicativo do Azure** , clique no ícone de seta para cima azul para implantar seu aplicativo no Azure.

![Implantar no aplicativo Web](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Você também pode implantar a partir da **paleta de comandos** (Ctrl + Shift + P) digitando ' implantar no aplicativo Web ' e **executando o serviço de Azure App: Comando implantar no aplicativo** Web.

1. Escolha o diretório que você abriu no momento, `myExpressApp`.

2. Escolha **criar novo aplicativo Web**.

3. Digite um nome global exclusivo para seu aplicativo Web e pressione ENTER. Os caracteres válidos para um nome de aplicativo são ' a-z ', ' 0-9 ' e '-'.

4. Escolha a **versão do node. js**, o LTS é recomendado.

    O canal de notificação mostra os recursos do Azure que estão sendo criados para seu aplicativo.

Clique em **Sim** quando solicitado a atualizar sua configuração para ser `npm install` executada no servidor de destino. Em seguida, seu aplicativo é implantado.

![Implantação configurada](./media/quickstart-nodejs/server-build.png)

Quando a implantação for iniciada, você será solicitado a atualizar seu espaço de trabalho para que as implantações posteriores sejam automaticamente direcionadas ao mesmo aplicativo Web do serviço de aplicativo. Escolha **Sim** para garantir que suas alterações sejam implantadas no aplicativo correto.

![Implantação configurada](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Certifique-se de que seu aplicativo está escutando na porta fornecida pela variável de ambiente `process.env.PORT`Port:.

### <a name="browse-the-website"></a>Navegar no site

Quando a implantação for concluída, clique em **procurar site** no prompt para exibir seu site da Web implantado recentemente.

### <a name="troubleshooting"></a>Resolução de problemas

Se você vir o erro **"você não tem permissão para exibir este diretório ou página."** , o aplicativo provavelmente não foi iniciado corretamente. Vá para a próxima seção e exiba a saída do log para localizar e corrigir o erro. Se você não conseguir corrigi-lo, entre em contato conosco clicando no botão **eu executei um problema** abaixo. Estamos felizes em ajudar!

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>Atualizando o site

Você pode implantar alterações nesse aplicativo usando o mesmo processo e escolhendo o aplicativo existente em vez de criar um novo.

## <a name="viewing-logs"></a>Exibindo logs

Nesta seção, você aprenderá a exibir (ou "Finalize") os logs do site em execução. Todas as chamadas `console.log` para no site são exibidas na janela saída em Visual Studio Code.

Localize o aplicativo no Gerenciador do **serviço de aplicativo do Azure** , clique com o botão direito do mouse no aplicativo e escolha **Exibir logs de streaming**.

Quando solicitado, escolha habilitar registro em log e reiniciar o aplicativo. Depois que o aplicativo for reiniciado, a janela VS Code saída será aberta com uma conexão com o fluxo de log.

![Exibir logs de streaming](./media/quickstart-nodejs/view-logs.png)

![Habilitar registro em log e reiniciar](./media/quickstart-nodejs/enable-restart.png)

Após alguns segundos, você verá uma mensagem indicando que você está conectado ao serviço de streaming de log.

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

Atualize a página algumas vezes no navegador para ver a saída do log.

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
```

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Passos Seguintes

Parabéns, você concluiu com êxito este guia de início rápido!

Em seguida, Confira as outras extensões do Azure.

* [BD do Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas de CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou então, faça tudo isso instalando o [pacote de nós para o pacote de extensão do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .
