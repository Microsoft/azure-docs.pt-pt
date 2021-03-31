---
title: Criar Funções Duráveis utilizando o portal Azure
description: Saiba como instalar a extensão de Funções Duradouras para Funções Azure para o desenvolvimento do portal.
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: b029fa246977dfe4210f6e8df242415f7e4103f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87081921"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Criar Funções Duráveis utilizando o portal Azure

A extensão [de Funções Duradouras](durable-functions-overview.md) para Funções Azure é fornecida no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Esta extensão deve ser instalada na sua aplicação de função. Este artigo mostra como instalar este pacote para que possa desenvolver funções duráveis no portal Azure.

> [!NOTE]
> 
> * Se estiver a desenvolver funções duráveis em C#, deve considerar o [desenvolvimento do Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Se estiver a desenvolver funções duráveis no JavaScript, deverá considerar o [desenvolvimento do Código do Estúdio Visual.](./quickstart-js-vscode.md)

## <a name="create-a-function-app"></a>Criar uma aplicação de funções

Deve ter uma aplicação de função para hospedar a execução de qualquer função. Uma aplicação de função permite-lhe agrupar as suas funções como uma unidade lógica para facilitar a gestão, implementação, escala e partilha de recursos. Pode criar uma aplicação .NET ou JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Por predefinição, a aplicação de função criada utiliza a versão 2.x do tempo de execução das Funções Azure. A extensão de Funções Duradouras funciona em ambas as versões 1.x e 2.x do tempo de funcionamento das Funções Azure em C#, e versão 2.x em JavaScript. No entanto, os modelos só estão disponíveis quando se destinam à versão 2.x do tempo de execução, independentemente do idioma escolhido.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instale o pacote npm de funções duráveis (apenas JavaScript)

Se estiver a criar Funções Duradouras JavaScript, terá de instalar o [ `durable-functions` pacote npm](https://www.npmjs.com/package/durable-functions):

1. Na página da aplicação da sua função, selecione **Ferramentas Avançadas** em **Ferramentas de Desenvolvimento** no painel esquerdo.

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Funcionalidades da plataforma escolham Kudu":::

2. Na página **Ferramentas Avançadas,** selecione **Go**.

3. Dentro da consola Kudu, selecione **a consola Debug** e, em seguida, **CMD**.

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Consola Kudu debug":::

3. A estrutura do diretório de ficheiros da sua aplicação de funções deve ser exibida. Navegue para a pasta `site/wwwroot`. A partir daí, pode carregar um `package.json` ficheiro arrastando-o e largando-o na janela do diretório de ficheiros. Uma amostra `package.json` é abaixo:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu carregar package.jsem":::

4. Uma vez `package.json` carregado, executa o `npm install` comando a partir da Consola de Execução Remota kudu.

   ![Kudu executar npm instalar](./media/durable-functions-create-portal/kudu-npm-install.png)
   
## <a name="create-an-orchestrator-function"></a>Criar uma função orquestradora

1. Na sua aplicação de funções, selecione **Funções** a partir do painel esquerdo e, em seguida, **selecione Adicione** no menu superior. 

1. No campo de pesquisa da página **Nova Função,** insira `durable` e, em seguida, escolha o modelo **de arranque FUNÇÕES Duradouras HTTP.**

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="Selecione funções duradouras http starter":::

1. Para o nome **da Nova Função,** `HttpStart` insira e, em seguida, **selecione Criar Função**.

   A função criada é usada para iniciar a orquestração.

1. Crie outra função na aplicação de função, desta vez utilizando o modelo **de orquestrador funções duráveis.** Nomeie a sua nova função de `HelloSequence` orquestração.

1. Crie uma terceira função nomeada `Hello` utilizando o modelo de atividade de **Funções Duradouras.**

## <a name="test-the-durable-function-orchestration"></a>Teste a orquestração de função durável

1. Volte para a função **HttpStart,** escolha **'Copiar' função url** e selecione o ícone **copy to clipboard** para copiar o URL. Utilize este URL para iniciar a função **HelloSequence.**

1. Utilize uma ferramenta HTTP como o Carteiro ou o CURL para enviar um pedido de POST para o URL que copiou. O exemplo a seguir é um comando cURL que envia um pedido DEM para a função durável:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence --header "Content-Length: 0"
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

1. Ligue para o `statusQueryGetUri` ponto final URI e verá o estado atual da função durável, que pode parecer com este exemplo:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continue a chamar o `statusQueryGetUri` ponto final até que o estado mude para **Concluído,** e vê uma resposta como o seguinte exemplo:

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
> [Saiba mais sobre padrões comuns de função durável](durable-functions-overview.md#application-patterns)
