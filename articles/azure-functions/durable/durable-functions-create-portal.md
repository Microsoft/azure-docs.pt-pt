---
title: Criar funções duráveis com o portal do Azure
description: Saiba como instalar a extensão de funções duráveis para as funções do Azure para desenvolvimento de portais.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: 1c60bd4dae6c279ccff637ff0aa798c48ebec6f1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108074"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Criar funções duráveis com o portal do Azure

O [funções duráveis](durable-functions-overview.md) extensão para as funções do Azure é fornecido no pacote de NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Esta extensão tem de estar instalada na sua aplicação de função. Este artigo mostra como instalar este pacote, para que pode desenvolver funções duráveis no portal do Azure.

> [!NOTE]
> 
> * Se estiver a desenvolver funções duráveis no C#, em vez disso, deve considerar [desenvolvimento do Visual Studio 2017](durable-functions-create-first-csharp.md).
> * Se estiver a desenvolver funções duráveis em JavaScript, em vez disso, deve considerar [desenvolvimento do Visual Studio Code](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Tem de ter uma aplicação de funções para alojar a execução de qualquer função. Uma aplicação de funções permite-lhe agrupar as suas funções como unidades lógicas para uma gestão mais fácil, implementação e partilha de recursos. Pode criar uma aplicação .NET ou JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Por predefinição, a aplicação de funções criada utiliza a versão 2.x do runtime das funções do Azure. A extensão de funções duráveis funciona em ambas as versões 1.x e 2.x do runtime das funções do Azure no C#e a versão 2.x em JavaScript. No entanto, modelos só estão disponíveis quando versão 2.x do runtime independentemente do idioma escolhido.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instalar o pacote de npm de funções durável (apenas para JavaScript)

Se estiver a criar funções duráveis do JavaScript, terá de instalar o [ `durable-functions` pacote npm](https://www.npmjs.com/package/durable-functions).

1. Selecione o nome da sua aplicação de função, seguido **funcionalidades de plataforma**, em seguida, **avançadas (Kudu) de ferramentas**.

   ![Funcionalidades de plataforma das funções escolha Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Dentro do console do Kudu, selecione **consola de depuração** , em seguida, **CMD**.

   ![Consola de depuração do kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Deverá apresentar a estrutura de diretórios de ficheiros da sua aplicação de função. Navegue para a pasta `site/wwwroot`. A partir daí, pode carregar um `package.json` ficheiro arrastando e soltando-o para a janela de diretório do ficheiro. Um exemplo `package.json` é abaixo:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.1.2"
      }
    }
    ```

   ![Kudu carregar Package. JSON](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Uma vez sua `package.json` é carregado, execute o `npm install` comando a partir da consola de execução remota de Kudu.

   ![Kudu executar a instalação de npm](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Criar uma função de orquestrador

1. Expanda a aplicação de funções e clique no botão **+**, junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Caso contrário, avance para o passo três.

   ![Página de início rápido das funções no portal do Azure](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/durable-functions-create-portal/add-first-function.png)

1. No campo de pesquisa, escreva `durable` e, em seguida, selecione o **starter de HTTP de funções durável** modelo.

1. Quando lhe for pedido, selecione **instalar** para instalar a extensão Azure DurableTask quaisquer dependências na function app. Apenas terá de instalar a extensão de uma vez para uma aplicação de funções de dar. Depois de instalar com êxito, selecione **Continuar**.

    ![Instalar as extensões de enlace](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Depois de concluída a instalação, nomeie a nova função `HttpStart` e escolha **criar**. A função criada é utilizada para iniciar a orquestração.

1. Criar outra função na function app, desta vez utilizando o **Orquestrador de funções duráveis** modelo. Nomear sua nova função de orquestração `HelloSequence`.

1. Criar uma terceira função chamada `Hello` utilizando o **atividade de funções durável** modelo.

## <a name="test-the-durable-function-orchestration"></a>Testar a orquestração de função durável

1. Volte para o **HttpStart** função, escolha **<> / obter URL da função** e **cópia** o URL. Utilize este URL para iniciar o **HelloSequence** função.

1. Utilize uma ferramenta HTTP como o Postman ou o cURL para enviar um pedido POST para o URL que copiou. O exemplo seguinte é um comando cURL que envia um pedido POST para a função durável:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Neste exemplo, `{your-function-app-name}` é o domínio que é o nome da sua aplicação de função. A mensagem de resposta contém um conjunto de pontos finais URI que pode utilizar para monitorizar e gerir a execução, o que é semelhante ao seguinte exemplo:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Chamar o `statusQueryGetUri` URI do ponto final e ver o estado atual da função durável, que pode ter um aspeto semelhante a este exemplo:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continuar a chamar o `statusQueryGetUri` ponto final até que o estado é alterado para **concluído**, e verá uma resposta semelhante ao seguinte exemplo:

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

Sua primeira função durável está agora em execução no Azure.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões comuns de função durável](durable-functions-concepts.md)
