---
title: Criar sua primeira função durável no Azure usando o JavaScript
description: Crie e publique uma função durável do Azure usando Visual Studio Code.
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: 94ba2830824c4a918e9451a9fc5140d422110370
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231303"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Criar sua primeira função durável em JavaScript

*Durable Functions* é uma extensão de [Azure Functions](../functions-overview.md) que permite que você escreva funções com estado em um ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

Neste artigo, você aprenderá a usar a extensão de Azure Functions de Visual Studio Code para criar e testar localmente uma função durável "Olá, mundo".  Essa função orquestrará e encadeará chamadas para outras funções. Em seguida, publique o código de função no Azure.

![Executando a função durável no Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale o [Visual Studio Code](https://code.visualstudio.com/download).

* Verifique se você tem a versão mais recente do [Azure Functions Core Tools](../functions-run-local.md).

* Em um computador Windows, verifique se o [emulador de armazenamento do Azure](../../storage/common/storage-use-emulator.md) está instalado e em execução. Em um computador Mac ou Linux, você deve usar uma conta de armazenamento do Azure real.

* Verifique se você tem a versão 8,0 ou uma versão posterior do [node. js](https://nodejs.org/) instalada.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Instalar o pacote Durable Functions NPM

1. Instale o pacote `durable-functions` NPM executando `npm install durable-functions` no diretório raiz do aplicativo de funções.

## <a name="creating-your-functions"></a>Criando suas funções

Agora, criaremos as três funções que você precisa para começar a Durable Functions: um iniciador de HTTP, um orquestrador e uma função de atividade. O iniciador HTTP iniciará toda a sua solução e o orquestrador irá distribuir o trabalho para várias funções de atividade.

### <a name="http-starter"></a>Iniciador de HTTP

Primeiro, crie uma função disparada por HTTP que inicia uma orquestração de função durável.

1. No *Azure: Functions*, escolha o ícone **criar função** .

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com seu projeto de aplicativo de funções e selecione o modelo de função de **início de HTTP Durable Functions** .

    ![Escolher o modelo de início de HTTP](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Deixe o nome padrão como `DurableFunctionsHttpStart` e pressione * * * * Enter * * e, em seguida, selecione Autenticação **anônima** .

    ![Escolher autenticação anónima](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Agora, criamos um ponto de entrada em nossa função durável. Vamos adicionar um orquestrador.

### <a name="orchestrator"></a>Orchestrator

Agora, criaremos um orquestrador para coordenar funções de atividade.

1. No *Azure: Functions*, escolha o ícone **criar função** .

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com seu projeto de aplicativo de funções e selecione o modelo de função **Durable Functions Orchestrator** . Deixe o nome como o padrão "DurableFunctionsOrchestrator"

    ![Escolher o modelo de orquestrador](./media/quickstart-js-vscode/create-function-choose-template.png)

Adicionamos um orquestrador para coordenar funções de atividade. Agora, vamos adicionar a função de atividade referenciada.

### <a name="activity"></a>Atividade

Agora, criaremos uma função de atividade para realmente realizar o trabalho da solução.

1. No *Azure: Functions*, escolha o ícone **criar função** .

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com seu projeto de aplicativo de funções e selecione o modelo função de **atividade de durable Functions** . Deixe o nome como o padrão "Olá".

    ![Escolher o modelo de atividade](./media/quickstart-js-vscode/create-function-choose-template.png)

Agora adicionamos todos os componentes necessários para iniciar uma orquestração e encadear as funções de atividade.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deve instalar estas ferramentas ao iniciar uma função do Visual Studio Code pela primeira vez.

1. Em um computador Windows, inicie o emulador de armazenamento do Azure e verifique se a propriedade **AzureWebJobsStorage** de *local. Settings. JSON* está definida como `UseDevelopmentStorage=true`.

    Para o emulador de armazenamento 5,8, certifique-se de que a propriedade **AzureWebJobsSecretStorageType** de local. Settings. JSON esteja definida como `files`. Em um computador Mac ou Linux, você deve definir a propriedade **AzureWebJobsStorage** como a cadeia de conexão de uma conta de armazenamento do Azure existente. Você criará uma conta de armazenamento mais adiante neste artigo.

2. Para testar a sua função, defina um ponto de interrupção no código de função e prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**. Se esta for a primeira vez que você usa Durable Functions, a extensão Durable Functions será instalada e a compilação poderá levar alguns segundos.

    > [!NOTE]
    > O JavaScript Durable Functions requer a versão **1.7.0** ou posterior da extensão **Microsoft. Azure. webjobs. Extensions. DurableTask** . Execute o seguinte comando na pasta raiz do seu aplicativo Azure Functions para instalar a extensão de Durable Functions `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Substitua `{functionName}` por `DurableFunctionsOrchestrator`.

5. Usando uma ferramenta como o [postmaster](https://www.getpostman.com/) ou a [ondulação](https://curl.haxx.se/), envie uma solicitação HTTP post para o ponto de extremidade da URL.

   A resposta é o resultado inicial da função HTTP que nos permite saber que a orquestração durável foi iniciada com êxito. Ainda não é o resultado final da orquestração. A resposta inclui algumas URLs úteis. Por enquanto, vamos consultar o status da orquestração.

6. Copie o valor da URL para `statusQueryGetUri` e cole-o na barra de endereços do navegador e execute a solicitação. Como alternativa, você também pode continuar a usar o postmaster para emitir a solicitação GET.

   A solicitação consultará a instância de orquestração do status. Você deve obter uma resposta eventual, que nos mostra que a instância foi concluída e inclui as saídas ou os resultados da função durável. Ele é semelhante a: 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. Para interromper a depuração, pressione **Shift + F5** em vs Code.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. O URL que chama a função acionada por HTTP deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. Você deve obter a mesma resposta de status que antes de usar o aplicativo publicado.

## <a name="next-steps"></a>Passos seguintes

Você usou Visual Studio Code para criar e publicar um aplicativo de funções duráveis do JavaScript.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões comuns de função durável](durable-functions-overview.md#application-patterns)