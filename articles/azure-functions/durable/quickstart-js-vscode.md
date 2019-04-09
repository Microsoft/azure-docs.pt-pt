---
title: Criar a sua primeira função durável no Azure com o JavaScript
description: Crie e publique uma função de durável do Azure com o Visual Studio Code.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf, cotresne, glenga
ms.openlocfilehash: 94906a75d323e3fb6c96f939e4fdcea60877508c
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056304"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Criar a sua primeira função durável em JavaScript

*Funções duráveis* é uma extensão da [as funções do Azure](../functions-overview.md) que permite que escreva funções com monitoração de estado num ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si.

Neste artigo, irá aprender a utilizar a extensão de funções de Azure de código do Visual Studio para criar localmente e testar uma função durável "hello world".  Esta função será orquestrar e em conjunto da cadeia de chamadas para outras funções. Em seguida, publique o código de função no Azure.

![Executar a função durável no Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale [Visual Studio Code](https://code.visualstudio.com/download).

* Verifique se tem as [ferramentas de Funções do Azure mais recentes](../functions-develop-vs.md#check-your-tools-version).

* Num computador Windows, certifique-se de que tem o [emulador de armazenamento do Azure](../../storage/common/storage-use-emulator.md) instalado e em execução. Num computador Mac ou Linux, tem de utilizar uma conta de armazenamento do Azure real.

* Certifique-se de que tem a versão 8.0 ou uma versão posterior do [node. js](https://nodejs.org/) instalado.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Instalar o pacote de npm de funções duráveis

1. Instalar o `durable-functions` pacote de npm executando `npm install durable-functions` no diretório de raiz da aplicação de função.

## <a name="create-a-starter-function"></a>Criar uma função de iniciante

Primeiro, crie uma função acionada por HTTP, que inicia uma orquestração de função durável.

1. De **Azure: As funções**, escolha o ícone de Create Function.

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com o projeto da aplicação de funções e selecione o modelo de função **acionador HTTP**.

    ![Escolher o modelo do acionador HTTP](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Escreva `HttpStart` para o nome da função e prima Enter, em seguida selecione a autenticação **Anónima**.

    ![Escolher autenticação anónima](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

    É criada uma função na linguagem que escolheu com o modelo de função acionada por HTTP.

4. Substitua Index com o abaixo JavaScript:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

5. Substitua Function com o abaixo JSON:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Agora, criamos um ponto de entrada em nossa função durável. Vamos adicionar um orquestrador.

## <a name="create-an-orchestrator-function"></a>Criar uma função de Orquestrador

Em seguida, criar outra função para ser o orchestrator. Utilizamos o modelo de função de Acionador HTTP para sua comodidade. O código de função em si é substituído pelo código do orchestrator.

1. Repita os passos da secção anterior para criar uma segunda função usando o modelo de Acionador HTTP. Desta vez chamar a função de `OrchestratorFunction`.

2. Abra o ficheiro Index js para a nova função e substitua os conteúdos com o código a seguir:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

3. Abra o ficheiro de Function e substituí-lo com o seguinte JSON:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Adicionámos um orquestrador para coordenar as funções de atividade. Vamos agora adicionar a função de atividade referenciada.

## <a name="create-an-activity-function"></a>Criar uma função de atividade

1. Repita os passos das secções anteriores para criar uma terceira função usando o modelo de Acionador HTTP. Mas, desta vez chamar a função de `E1_SayHello`.

2. Abra o ficheiro Index js para a nova função e substitua os conteúdos com o código a seguir:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

3. Substitua Function com o abaixo JSON:

    [!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

Agora adicionámos todos os componentes necessários para começar uma orquestração e a cadeia das funções de atividade em conjunto.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deve instalar estas ferramentas ao iniciar uma função do Visual Studio Code pela primeira vez.  

1. Num computador Windows, inicie o emulador de armazenamento do Azure e certifique-se de que o **AzureWebJobsStorage** propriedade Settings é definida como `UseDevelopmentStorage=true`. 

    Para 5.8 do emulador de armazenamento Certifique-se de que o **AzureWebJobsSecretStorageType** propriedade Settings é definida como `files`. Num computador Mac ou Linux, tem de definir o **AzureWebJobsStorage** propriedade para a cadeia de ligação de uma conta de armazenamento do Azure existente. Criar uma conta de armazenamento neste artigo.

2. Para testar a sua função, defina um ponto de interrupção no código de função e prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**. Se esta for a primeira vez com as funções durável, a extensão de funções durável é instalada e a compilação poderá demorar alguns segundos.

    > [!NOTE]
    > Funções duráveis do JavaScript é necessária a versão **1.7.0** ou superior da **Microsoft.Azure.WebJobs.Extensions.DurableTask** extensão. Verificar a versão da extensão de funções duráveis no seu `extensions.csproj` ficheiro cumpre este requisito. Se não existir, pare a aplicação de funções, alterar a versão e pressione F5 para reiniciar a aplicação de funções.

3. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Substitua `{functionName}` por `OrchestratorFunction`.

5. Usando uma ferramenta como [Postman](https://www.getpostman.com/) ou [cURL](https://curl.haxx.se/), enviar um pedido de HTTP POST para o ponto de final do URL.

    A resposta é o resultado inicial da função HTTP nos informar a orquestração durável foi iniciado com êxito.  Ainda não é o resultado final da orquestração.  A resposta inclui alguns URLs útil.  Por enquanto, vamos consultar o estado da orquestração.

6. Copie o valor de URL para `statusQueryGetUri`, cole-o na barra de endereço do navegador e execute o pedido.

    O pedido irá consultar a instância de orquestração para o estado. Obterá uma resposta eventual que é semelhante ao seguinte.  Isto mostra-na instância tiver sido concluída e inclui as saídas ou os resultados da função durável.

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

7. Para parar a depuração, prima **Shift + F5** no VS Code.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. O URL que chama a função acionada por HTTP deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. Deve obter a mesma resposta como Estado, quando utilizar a aplicação publicada.

## <a name="next-steps"></a>Passos Seguintes

Utilizou o Visual Studio Code para criar e publicar uma aplicação de funções durável de JavaScript.

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões comuns de função durável](durable-functions-concepts.md)
