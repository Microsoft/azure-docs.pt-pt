---
title: Criar Funções Duráveis utilizando o portal Azure
description: Saiba como instalar a extensão de Funções Duráveis para funções azure para desenvolvimento do portal.
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: 7aa283480d95693e2630e24d5642fc7cb909b34b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848765"
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

Se estiver a criar Funções Duráveis JavaScript, terá de instalar o [ `durable-functions` pacote npm:](https://www.npmjs.com/package/durable-functions)

1. A partir da página da sua aplicação de funções, selecione **Ferramentas Avançadas** sob **Ferramentas** de Desenvolvimento no painel esquerdo.

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Funcionalidades da plataforma escolha Kudu":::

2. Na página **Ferramentas Avançadas,** selecione **Go**.

3. Dentro da consola Kudu, selecione **consola Debug**, e depois **CMD**.

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Consola Kudu debug":::

3. A estrutura de diretório de ficheiros da sua aplicação de funções deve ser exibida. Navegue para a pasta `site/wwwroot`. A partir daí, pode fazer o upload de um `package.json` ficheiro arrastando-o e largando-o na janela do diretório de ficheiros. Uma amostra `package.json` está abaixo:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Pacote de upload kudu.json":::

4. Assim que o seu `package.json` estiver carregado, execute o `npm install` comando a partir da Consola de Execução Remota kudu.

   ![Kudu executar instalação npm](./media/durable-functions-create-portal/kudu-npm-install.png)
   
5. Por último, (ativar o modo de https://docs.microsoft.com/en-us/azure/azure-functions/durable/quickstart-js-vscode#enable-compatibility-mode-1 compatibilidade)[ adicionando uma definição de aplicação `FUNCTIONS_V2_COMPATIBILITY_MODE` com valor de `true` .

## <a name="create-an-orchestrator-function"></a>Criar uma função orquestradora

1. Na sua aplicação de funções, selecione **Funções** do painel esquerdo e, em seguida, **selecione Adicionar** a partir do menu superior. 

1. No campo de pesquisa da página **New Function,** introduza `durable` e, em seguida, escolha o modelo de arranque HTTP **funções duráveis.**

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="Selecione Funções Duráveis HTTP starter":::

1. Para o nome **New Function,** introduza `HttpStart` , e, em seguida, selecione **Criar Função**.

   A função criada é usada para iniciar a orquestração.

1. Crie outra função na aplicação de funções, desta vez utilizando o modelo **de orquestrador de Funções Duráveis.** Diga o nome da sua nova função de `HelloSequence` orquestração.

1. Crie uma terceira função nomeada utilizando o modelo de atividade das `Hello` **Funções Duráveis.**

## <a name="test-the-durable-function-orchestration"></a>Teste a orquestração de funções duráveis

1. Volte à função **HttpStart,** escolha **'Get function Url'**, e selecione o ícone **de cópia para** copiar o URL. Usa este URL para iniciar a função **HelloSequence.**

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

1. Ligue para o `statusQueryGetUri` ponto final URI e verá o estado atual da função durável, que pode parecer este exemplo:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continue a ligar para o `statusQueryGetUri` ponto final até que o estado mude para **Concluído,** e verá uma resposta como o seguinte exemplo:

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
