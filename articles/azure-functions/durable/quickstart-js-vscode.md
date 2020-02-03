---
title: Crie a sua primeira função durável em Azure usando javaScript
description: Crie e publique uma Função Durável Azure utilizando o Código do Estúdio Visual.
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: b0a1d1a9305f6de2a072ee1ded310d8de174436b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845722"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Crie a sua primeira função durável no JavaScript

*Durable Functions* é uma extensão de [Azure Functions](../functions-overview.md) que permite que você escreva funções com estado em um ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

Neste artigo, aprende-se a usar a extensão visual do Código de Código Azure para criar e testar localmente uma função durável "hello world".  Esta função irá orquestrar e acorrentar chamadas em conjunto para outras funções. Em seguida, publique o código de função no Azure.

![Executando a função durável no Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale o Código do [Estúdio Visual](https://code.visualstudio.com/download).

* Certifique-se de que tem a versão mais recente das [Ferramentas Core funções do Azure](../functions-run-local.md).

* Num computador Windows, verifique se tem o [Emulador](../../storage/common/storage-use-emulator.md) de Armazenamento Azure instalado e em execução. Num computador Mac ou Linux, deve utilizar uma conta de armazenamento Azure real.

* Certifique-se de que tem a versão 8.0 ou uma versão posterior do [Node.js](https://nodejs.org/) instalada.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Crie o seu projeto local 

Nesta secção, você usa o Código de Estúdio Visual para criar um projeto local de Funções Azure. 

1. Em Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Create new project...`.

1. Escolha um local de diretório para o espaço de trabalho do projeto e escolha **selecionar**.

    > [!NOTE]
    > Essas etapas foram projetadas para serem concluídas fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Seguindo as instruções, forneça as seguintes informações para a sua língua desejada:

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um idioma para o seu projeto de aplicação de funções | Javascript | Crie um projeto local de Funções Node.js. |
    | selecionar uma versão | Funções Azure v2 | Só se vê esta opção quando as Ferramentas Core ainda não estão instaladas. Neste caso, as Ferramentas Core são instaladas na primeira vez que executa a aplicação. |
    | Selecione um modelo para a primeira função do seu projeto | Acionador HTTP | Crie uma função ativada em HTTP na nova aplicação de funções. |
    | Fornecer um nome de função | HttpTrigger | Pressione Introduza para utilizar o nome predefinido. |
    | Nível de autorização | Função | O nível de autorização `function` requer que forneça uma chave de acesso ao ligar para o ponto final http da sua função. Isto torna mais difícil o acesso a um ponto final não seguro. Para saber mais, consulte [as chaves de autorização.](../functions-bindings-http-webhook.md#authorization-keys)  |
    | Selecione como pretende abrir o seu projeto | Adicione ao espaço de trabalho | Cria a aplicação de funções no espaço de trabalho atual. |

O Visual Studio Code instala as Ferramentas Core funções Do Azure, se necessário. Também cria um projeto de aplicação de funções num novo espaço de trabalho. Este projeto contém os ficheiros de configuração [host.json](../functions-host-json.md) e [local.settings.json.](../functions-run-local.md#local-settings-file) Também cria uma pasta HttpExample que contém o ficheiro de [definição function.json](../functions-reference-node.md#folder-structure) e o [ficheiro index.js](../functions-reference-node.md#exporting-a-function), um ficheiro Node.js que contém o código de função.

Um ficheiro package.json também é criado na pasta raiz.

## <a name="install-the-durable-functions-npm-package"></a>Instale o pacote NPM Funções Duráveis

1. Instale o pacote `durable-functions` npm executando `npm install durable-functions` no diretório raiz da aplicação de funções.

## <a name="creating-your-functions"></a>Criar as suas funções

Vamos agora criar as três funções que precisa para começar com Funções Duráveis: um arranque HTTP, um orquestrador e uma função de atividade. O arranque http iniciará toda a sua solução, e o orquestrador irá enviar trabalho para várias funções de atividade.

### <a name="http-starter"></a>INÍCIO HTTP

Primeiro, crie uma função ativada http que inicie uma orquestração de funções duráveis.

1. A partir de *Azure: Funções,* escolha o ícone **Criar Função.**

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com o projeto da aplicação de funções e selecione o modelo de função de arranque de **funções HTTP Funções Duráveis.**

    ![Escolha o modelo de arranque http](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Deixe o nome predefinido, como `DurableFunctionsHttpStart` e prima ** **Enter**, em seguida, selecione **anónimo** autenticação.

    ![Escolher autenticação anónima](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Criamos agora um ponto de entrada na nossa Função Durável. Vamos adicionar um orquestrador.

### <a name="orchestrator"></a>Orchestrator

Vamos criar um orquestrador para coordenar as funções de atividade.

1. A partir de *Azure: Funções,* escolha o ícone **Criar Função.**

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com o projeto da aplicação de funções e selecione o modelo de função de orquestrador de **Funções Duráveis.** Deixe o nome como "DurableFunctionsOrchestrator"

    ![Escolha o modelo de orquestrador](./media/quickstart-js-vscode/create-function-choose-template.png)

Adicionámos um orquestrador para coordenar as funções de atividade. Vamos agora adicionar a função de atividade referenciada.

### <a name="activity"></a>Atividade

Agora, vamos criar uma função de atividade para realmente realizar o trabalho da solução.

1. A partir de *Azure: Funções,* escolha o ícone **Criar Função.**

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com o projeto da aplicação de funções e selecione o modelo de função de função de função de **Funções Duráveis.** Deixe o nome como "Olá" padrão.

    ![Escolha o modelo de atividade](./media/quickstart-js-vscode/create-function-choose-template.png)

Adicionámos todos os componentes necessários para iniciar uma orquestração e correntes de funções de atividade.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deve instalar estas ferramentas ao iniciar uma função do Visual Studio Code pela primeira vez.

1. Num computador Windows, inicie o Emulador de Armazenamento Azure e certifique-se de que a propriedade **AzureWebJobsStorage** de *local.settings.json* está definida para `UseDevelopmentStorage=true`.

    Para o Emulador de Armazenamento 5.8 certifique-se de que a propriedade **AzureWebJobsSecretStorageType** de local.settings.json está definida para `files`. Num computador Mac ou Linux, deve definir a propriedade **AzureWebJobsStorage** na cadeia de ligação de uma conta de armazenamento Azure existente. Cria uma conta de armazenamento mais tarde neste artigo.

2. Para testar a sua função, defina um ponto de interrupção no código de função e prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**. Se for a primeira vez que utiliza funções duráveis, a extensão de funções duráveis está instalada e a construção pode demorar alguns segundos.

    > [!NOTE]
    > As Funções Duráveis JavaScript requerem a versão **1.7.0** ou maior da **Microsoft.Azure.WebJobs.Extensions.DurableTask** extension. Execute o seguinte comando a partir da pasta raiz da sua aplicação Funções Azure para instalar a extensão funções duráveis `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Substitua `{functionName}` por `DurableFunctionsOrchestrator`.

5. Utilizando uma ferramenta como [o Carteiro](https://www.getpostman.com/) ou [cURL,](https://curl.haxx.se/)envie um pedido HTTP POST para o ponto final do URL.

   A resposta é o resultado inicial da função HTTP que nos permite saber que a orquestração durável foi iniciada com êxito. Ainda não é o resultado final da orquestração. A resposta inclui algumas URLs úteis. Por enquanto, vamos consultar o status da orquestração.

6. Copie o valor de URL para `statusQueryGetUri` e cole-o na barra de endereços do navegador e execute o pedido. Em alternativa, também pode continuar a utilizar o Carteiro para emitir o pedido GET.

   A solicitação consultará a instância de orquestração do status. Deve obter uma resposta eventual, o que nos mostra que a instância já terminou, e inclui as saídas ou resultados da função durável. Parece que: 

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

7. Para parar de depurar, prima **Shift + F5** no Código VS.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. O URL que chama a função acionada por HTTP deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. Você deve obter a mesma resposta de status que antes de usar o aplicativo publicado.

## <a name="next-steps"></a>Passos seguintes

Usou o Código do Estúdio Visual para criar e publicar uma aplicação de função durável JavaScript.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões comuns de função durável](durable-functions-overview.md#application-patterns)