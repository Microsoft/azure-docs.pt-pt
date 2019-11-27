---
title: Criar Durable Functions usando o portal do Azure
description: Saiba como instalar a extensão de Durable Functions para Azure Functions para desenvolvimento de Portal.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: eaa241eff6e1c359045a0ea3d8871fde6c60a059
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231486"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Criar Durable Functions usando o portal do Azure

A extensão de [Durable Functions](durable-functions-overview.md) para Azure Functions é fornecida no pacote NuGet [Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Essa extensão deve ser instalada em seu aplicativo de funções. Este artigo mostra como instalar esse pacote para que você possa desenvolver funções duráveis no portal do Azure.

> [!NOTE]
> 
> * Se você estiver desenvolvendo funções duráveis no C#, considere o desenvolvimento do [Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Se você estiver desenvolvendo funções duráveis em JavaScript, em vez disso, considere [Visual Studio Code desenvolvimento](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Você deve ter um aplicativo de funções para hospedar a execução de qualquer função. Um aplicativo de funções permite que você agrupe suas funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Você pode criar um aplicativo .NET ou JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Por padrão, o aplicativo de funções criado usa a versão 2. x do tempo de execução de Azure Functions. A extensão Durable Functions funciona em ambas as versões 1. x e 2. x do tempo de execução C#de Azure Functions no e na versão 2. x em JavaScript. No entanto, os modelos só estão disponíveis ao direcionar para a versão 2. x do tempo de execução, independentemente da linguagem escolhida.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instalar o pacote NPM de funções duráveis (somente JavaScript)

Se você estiver criando Durable Functions de JavaScript, será necessário instalar o [pacote`durable-functions` NPM](https://www.npmjs.com/package/durable-functions).

1. Selecione o nome do aplicativo de funções, seguido por **recursos de plataforma**e **ferramentas avançadas (kudu)** .

   ![Recursos da plataforma de funções escolha kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. No console do kudu, selecione **console de depuração** e, em seguida, **cmd**.

   ![Console de depuração do kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. A estrutura do diretório de arquivos do seu aplicativo de funções deve ser exibida. Navegue para a pasta `site/wwwroot`. A partir daí, você pode carregar um arquivo de `package.json` arrastando-o e soltando-o na janela do diretório de arquivos. Um `package.json` de exemplo está abaixo:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu carregar pacote. JSON](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Depois que o `package.json` for carregado, execute o comando `npm install` no console de execução remota do kudu.

   ![Kudu executar a instalação do NPM](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Criar uma função de orquestrador

1. Expanda a aplicação de funções e clique no botão **+** , junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Caso contrário, avance para o passo três.

   ![Página de início rápido das funções no portal do Azure](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/durable-functions-create-portal/add-first-function.png)

1. No campo de pesquisa, digite `durable` e, em seguida, escolha o modelo de **início de HTTP Durable Functions** .

1. Quando solicitado, selecione **instalar** para instalar a extensão DurableTask do Azure quaisquer dependências no aplicativo de funções. Você só precisa instalar a extensão uma vez para um aplicativo de função de permissão. Depois de instalar com êxito, selecione **Continuar**.

    ![Instalar as extensões de enlace](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Após a conclusão da instalação, nomeie a nova função `HttpStart` e escolha **criar**. A função criada é usada para iniciar a orquestração.

1. Crie outra função no aplicativo de funções, desta vez usando o modelo **Durable Functions Orchestrator** . Nomeie sua nova função de orquestração `HelloSequence`.

1. Crie uma terceira função chamada `Hello` usando o modelo de **atividade de durable Functions** .

## <a name="test-the-durable-function-orchestration"></a>Testar a orquestração de função durável

1. Volte para a função **HttpStart** , escolha **</> obter URL de função** e **Copie** a URL. Use essa URL para iniciar a função **HelloSequence** .

1. Use uma ferramenta HTTP como o postmaster ou a ondulação para enviar uma solicitação POST para a URL que você copiou. O exemplo a seguir é um comando de ondulação que envia uma solicitação POST para a função durável:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Neste exemplo, `{your-function-app-name}` é o domínio que é o nome do seu aplicativo de funções. A mensagem de resposta contém um conjunto de pontos de extremidade de URI que você pode usar para monitorar e gerenciar a execução, que é semelhante ao exemplo a seguir:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Chame o URI do ponto de extremidade `statusQueryGetUri` e você verá o status atual da função durável, que pode ser semelhante a este exemplo:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continue chamando o ponto de extremidade `statusQueryGetUri` até que o status seja alterado para **concluído**e você veja uma resposta como o exemplo a seguir:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Sua primeira função durável agora está ativa e em execução no Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões comuns de função durável](durable-functions-overview.md#application-patterns)
