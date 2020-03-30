---
title: Criar Funções Duráveis utilizando o portal Azure
description: Saiba como instalar a extensão de Funções Duráveis para funções azure para desenvolvimento do portal.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769647"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Criar Funções Duráveis utilizando o portal Azure

A extensão [de funções duráveis](durable-functions-overview.md) para funções Azure é fornecida no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Esta extensão deve ser instalada na sua aplicação de funções. Este artigo mostra como instalar este pacote para que possa desenvolver funções duráveis no portal Azure.

> [!NOTE]
> 
> * Se está a desenvolver funções duráveis em C#, deve considerar o desenvolvimento do [Visual Studio 2019.](durable-functions-create-first-csharp.md)
> * Se estiver a desenvolver funções duráveis no JavaScript, deve considerar o desenvolvimento do [Código do Estúdio Visual.](./quickstart-js-vscode.md)

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Deve ter uma aplicação de função para acolher a execução de qualquer função. Uma aplicação de função permite-lhe agrupar as suas funções como uma unidade lógica para uma gestão mais fácil, implementação, escalae e partilha de recursos. Pode criar uma aplicação .NET ou JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Por padrão, a aplicação de função criada utiliza a versão 2.x do tempo de funcionamento das Funções Azure. A extensão Das Funções Duráveis funciona em ambas as versões 1.x e 2.x das Funções Azure em C#e versão 2.x no JavaScript. No entanto, os modelos só estão disponíveis quando se orienta a versão 2.x do tempo de execução, independentemente do idioma escolhido.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instale o pacote npm de funções duráveis (apenas JavaScript)

Se estiver a criar Funções Duráveis JavaScript, terá de instalar o [ `durable-functions` pacote npm](https://www.npmjs.com/package/durable-functions).

1. Selecione o nome da sua aplicação de função, seguida de **Funcionalidades**da Plataforma, em seguida, **ferramentas avançadas (Kudu)**.

   ![Funcionalidades da plataforma escolha Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Dentro da consola Kudu, selecione a **consola Debug** e a **CMD**.

   ![Consola Kudu debug](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. A estrutura de diretório de ficheiros da sua aplicação de funções deve ser exibida. Navegue para a pasta `site/wwwroot`. A partir daí, `package.json` pode fazer o upload de um ficheiro arrastando-o e largando-o na janela do diretório de ficheiros. Uma `package.json` amostra está abaixo:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Pacote de upload kudu.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Assim `package.json` que o seu `npm install` estiver carregado, execute o comando a partir da Consola de Execução Remota kudu.

   ![Kudu executar instalação npm](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Criar uma função orquestradora

1. Expanda a sua **+** aplicação de função e clique no botão ao lado das **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Caso contrário, avance para o passo três.

   ![Início rápido das funções no portal do Azure](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/durable-functions-create-portal/add-first-function.png)

1. No campo de `durable` pesquisa, escreva e, em seguida, escolha o modelo de **arranque HTTP funções duráveis.**

1. Quando solicitado, selecione **Instalar** para instalar a extensão Azure DurableTask e quaisquer dependências na aplicação de função. Só tem de instalar a extensão uma vez para uma determinada aplicação de funções. Depois de instalar com êxito, selecione **Continuar**.

    ![Instalar as extensões de enlace](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Depois de a instalação estar `HttpStart` concluída, nomeie a nova função e escolha **Criar**. A função criada é usada para iniciar a orquestração.

1. Crie outra função na aplicação de funções, desta vez utilizando o modelo de Orquestrador de **Funções Duráveis.** Diga o nome `HelloSequence`da sua nova função de orquestração.

1. Crie uma `Hello` terceira função nomeada utilizando o modelo de atividade de **funções duráveis.**

## <a name="test-the-durable-function-orchestration"></a>Teste a orquestração de funções duráveis

1. Volte à função **HttpStart,** escolha **</> Obtenha** URL de função e **copie** o URL. Usa este URL para iniciar a função **HelloSequence.**

1. Utilize uma ferramenta HTTP como o Carteiro ou cURL para enviar um pedido post para o URL que copiou. O exemplo seguinte é um comando cURL que envia um pedido POST para a função durável:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Neste exemplo, `{your-function-app-name}` é o domínio que é o nome da sua aplicação de função. A mensagem de resposta contém um conjunto de pontos finais URI que pode utilizar para monitorizar e gerir a execução, e que pode ser semelhante ao seguinte exemplo:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Ligue `statusQueryGetUri` para o ponto final URI e verá o estado atual da função durável, que pode parecer este exemplo:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continue a `statusQueryGetUri` ligar para o ponto final até que o estado mude para **Concluído,** e verá uma resposta como o seguinte exemplo:

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

A sua primeira função durável está agora a funcionar em Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça os padrões comuns de função durável](durable-functions-overview.md#application-patterns)
