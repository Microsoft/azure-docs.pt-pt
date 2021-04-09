---
title: 'Quickstart: Criar uma aplicação web Node.js'
description: Implemente o seu primeiro Node.js Hello World para o Serviço de Aplicações Azure em minutos.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748864"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar uma aplicação Web Node.js no Azure

Começar com <abbr title="Um serviço baseado em HTTP para hospedar aplicações web, APIs REST e aplicações móveis de back-end.">Serviço de Aplicações do Azure</abbr> criando uma aplicação Node.js/Express localmente usando o Visual Studio Code e, em seguida, implantando a aplicação para a nuvem Azure. Porque usas um <abbr title="No Azure App Service, um nível base em que a sua aplicação funciona nos mesmos VMs que outras aplicações, incluindo as aplicações de outros clientes. Este nível destina-se ao desenvolvimento e ao teste.">nível livre</abbr>Não tem custos para completar este arranque rápido.

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

- Uma conta Azure com um ativo <abbr title="Uma subscrição do Azure é um contentor lógico utilizado para aprovisionar recursos no Azure. Armazena os detalhes de todos os seus recursos, como máquinas virtuais (VMs), bases de dados e muito mais.">subscrição</abbr>. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)
- <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
- [Node.js e npm.](https://nodejs.org) Verifique o comando `node --version` para verificar se Node.js está instalada.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Serviço de Aplicações Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) para Código de Estúdio Visual.

[Reportar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. Clone e executar uma aplicação de Node.js local

1. No seu computador local, abra um terminal e clone o repositório de amostras:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navegue na nova pasta de aplicações:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Instalar as dependências:

    ```bash
    npm install
    ```

1. Inicie a aplicação para testá-la localmente:

    ```bash
    npm start
    ```
    
1. Abra o seu navegador e navegue para `http://localhost:1337` . O navegador deve exibir "Hello World!".

1. Pressione <kbd>ctrl</kbd>  +  <kbd>C</kbd> no terminal para parar o servidor.

[Reportar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. Visualização de registos do Código do Estúdio Visual

Ver o <abbr title="Quaisquer chamadas `console.log` na aplicação são apresentadas na janela de saída no Código do Estúdio Visual.">log</abbr> da aplicação de Serviço de Aplicações em execução.

1. Encontre a aplicação no explorador do **Serviço de APLICAÇÕES AZURE,** clique com o botão direito no nome da aplicação e escolha **Iniciar Registos de Streaming.**

1. A janela de saída do Código do Estúdio Visual abre-se.

    ![Ver Registos de Streaming](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Screenshot do pedido do Código VS para ativar a gravação de ficheiros e reiniciar a aplicação web, com o botão sim selecionado.":::

1. Após alguns segundos, verá uma mensagem indicando que está ligado ao serviço de streaming de registos. 
1. Refresque a página algumas vezes para ver mais atividade.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[Reportar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Limpar recursos

Encontre a aplicação no explorador do **Serviço de APLICAÇÕES AZURE,** clique com o botão direito no nome da aplicação e escolha **Delete**. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="Encontre a aplicação no explorador '**'AZURE APP SERVICE'**', clique com o botão direito no nome da aplicação e escolha 'Delete'":::

## <a name="next-steps"></a>Passos seguintes

Parabéns, conseguiste completar esta rapidinha! Pode implementar alterações nesta aplicação utilizando o mesmo processo e escolhendo a aplicação existente em vez de criar uma nova.

Em seguida, confira as outras extensões do Azure.

* [BD do Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha-os todos instalando o pacote de extensões [Node para Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)