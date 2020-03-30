---
title: 'Quickstart: Crie uma app Linux Node.js'
description: Inicie-se com aplicações Linux no Serviço de Aplicações Azure, implementando a sua primeira aplicação Node.js para um contentor Linux no App Service.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: fb79f4b87d8ddc5303fea834c81329315401464a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74688978"
---
# <a name="create-a-nodejs-app-in-azure"></a>Crie uma app Node.js em Azure

O Serviço de Aplicações do Azure oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este quickstart mostra como implementar uma aplicação Node.js para o Azure App Service.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma conta Azure, [inscreva-se hoje](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) para uma conta gratuita com 200 dólares em créditos Azure para experimentar qualquer combinação de serviços.

Você precisa do [Código de Estúdio Visual](https://code.visualstudio.com/) instalado juntamente com [Node.js e npm](https://nodejs.org/en/download), o gestor de pacotes Node.js.

Também terá de instalar a extensão do Serviço de [Aplicações Azure,](vscode:extension/ms-azuretools.vscode-azureappservice)que pode utilizar para criar, gerir e implementar aplicações Web Linux na Plataforma Azure como serviço (PaaS).

### <a name="sign-in"></a>Iniciar sessão

Assim que a extensão estiver instalada, inicie sessão na sua conta Azure. Na Barra de Atividades, selecione o logótipo Azure para mostrar o explorador de **SERVIÇO SAAl AZURE.** Selecione **iniciar sessão no Azure...** e siga as instruções.

![assinar em Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Resolução de problemas

Se vir o erro **"Não consigo encontrar subscrição com nome [ID de subscrição]",** pode ser porque está por trás de um proxy e incapaz de chegar à API Azure. Configure `HTTP_PROXY` `HTTPS_PROXY` e configure variáveis ambientais `export`com a informação de procuração no seu terminal utilizando .

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se a definição das variáveis ambientais não corrigir o problema, contacte-nos selecionando o **i encontrei um** botão de emissão abaixo.

### <a name="prerequisite-check"></a>Verificação pré-requisito

Antes de continuar, certifique-se de que tem todos os pré-requisitos instalados e configurados.

No Código VS, deverá consultar o seu endereço de e-mail Azure na Barra de Estado e a sua subscrição no explorador de **SERVIÇOS DE APLICAÇÕES AZURE.**

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Crie a sua aplicação Node.js

Em seguida, crie uma aplicação Node.js que pode ser implantada para a Nuvem. Este quickstart utiliza um gerador de aplicação para eliminar rapidamente a aplicação a partir de um terminal.

> [!TIP]
> Se já tiver concluído o [tutorial nonódo.js,](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)pode saltar para a frente para [O Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Andaime uma nova aplicação com o Gerador Expresso

[O Expresso](https://www.expressjs.com) é um quadro popular para a construção e execução de aplicações node.js. Pode andaime (criar) uma nova aplicação Express utilizando a ferramenta [Express Generator.](https://expressjs.com/en/starter/generator.html) O Gerador Expresso é enviado como um módulo npm e pode ser executado diretamente `npx`(sem instalação) utilizando a ferramenta de linha de comando npm .

```bash
npx express-generator myExpressApp --view pug --git
```

Os `--view pug --git` parâmetros dizem ao gerador para usar o `jade`motor do modelo `.gitignore` [pug](https://pugjs.org/api/getting-started.html) (anteriormente conhecido como) e para criar um ficheiro.

Para instalar todas as dependências da aplicação, vá `npm install`à nova pasta e corra .

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Executar a aplicação

Em seguida, certifique-se de que a aplicação está em funcionamento. A partir do terminal, `npm start` inicie a aplicação utilizando o comando para iniciar o servidor.

```bash
npm start
```

Agora, abra o seu [http://localhost:3000](http://localhost:3000)navegador e navegue para onde deve ver algo assim:

![Aplicação Expresso de Execução](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Implementar no Azure

Nesta secção, implementa a sua aplicação Node.js utilizando o Vs Code e a extensão do Serviço de Aplicações Azure. Este quickstart utiliza o modelo de implementação mais básico onde a sua aplicação é zipped e implantada para uma Web App Azure no Linux.

### <a name="deploy-using-azure-app-service"></a>Implementar utilizando o Serviço de Aplicações Azure

Primeiro, abra a pasta de aplicações no Código VS.

```bash
code .
```

No explorador de **SERVIÇO SACA DO AZURE,** selecione o ícone de seta azul para implantar a sua aplicação para o Azure.

![Implemente para web app](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Também pode ser implantado a partir da Paleta de **Comando** (CTRL + SHIFT + P) digitando 'deploy to web app' e executando o Serviço de **Aplicações Azure: Implementar para** o comando da Web App.

1. Escolha o diretório que tem `myExpressApp`abertamente.

1. Escolha **criar uma nova Aplicação Web**, que se implementa para o Serviço de Aplicações no Linux por padrão.

1. Digite um nome globalmente único para a sua Web App e prima ENTER. Os caracteres válidos para um nome de aplicação são 'a-z', '0-9', e '-'.

1. Escolha a sua **versão Node.js**, LTS é recomendado.

    O canal de notificação mostra os recursos Do Azure que estão a ser criados para a sua aplicação.

1. Selecione **Sim** quando solicitado para `npm install` atualizar a sua configuração para ser executado no servidor de destino. A sua aplicação é então implantada.

    ![Implantação configurada](./media/quickstart-nodejs/server-build.png)

1. Quando a implementação começar, é-lhe solicitado que atualize o seu espaço de trabalho para que as implementações posteriores direcionem automaticamente a mesma Aplicação Web do Serviço de Aplicações. Escolha **Sim** para garantir que as suas alterações estão implementadas na aplicação correta.

    ![Implantação configurada](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Certifique-se de que a sua aplicação está `process.env.PORT`a ouvir a porta fornecida pela variável ambiente PORT: .

### <a name="browse-the-app-in-azure"></a>Navegue na app em Azure

Assim que a implementação estiver concluída, **selecione 'Navegar website'** no pedido para visualizar a sua aplicação web recentemente implementada.

### <a name="troubleshooting"></a>Resolução de problemas

Se vir o erro **"Não tem permissão para ver este diretório ou página."** Dirija-se à secção seguinte e veja a saída de registo para encontrar e corrigir o erro. Se não conseguir consertá-lo, contacte-nos selecionando o **i encontrei um** botão de problema abaixo. Estamos felizes em ajudar!

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Atualizar a aplicação

Pode implementar alterações nesta aplicação utilizando o mesmo processo e escolhendo a app existente em vez de criar uma nova.

## <a name="viewing-logs"></a>Registos de visualização

Nesta secção, aprende-se a visualizar (ou "cauda") os registos da aplicação do Serviço de Aplicações em execução. Quaisquer `console.log` chamadas para a aplicação são apresentadas na janela de saída no Visual Studio Code.

Encontre a aplicação no explorador de **SERVIÇO SAC,** clique na aplicação e escolha **'Ver Registos de Streaming'.**

Quando solicitado, opte por ativar a exploração e reiniciar a aplicação. Uma vez reiniciada a aplicação, a janela de saída do Código VS abre-se com uma ligação ao fluxo de registo.

![Ver registos de streaming](./media/quickstart-nodejs/view-logs.png)

![Ativar a exploração madeireira e reiniciar](./media/quickstart-nodejs/enable-restart.png)

Após alguns segundos, verá uma mensagem indicando que está ligado ao serviço de streaming de log. Refresque a página algumas vezes para ver mais atividade.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

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
