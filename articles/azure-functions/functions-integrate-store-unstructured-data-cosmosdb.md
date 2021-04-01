---
title: Armazenar dados não estruturados utilizando Azure Cosmos DB e Funções
description: Armazenar dados não estruturados usando as funções do Azure e o Cosmos DB
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5bc3895cb219338acde492b871dce806db70622b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91661164"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Armazenar dados não estruturados usando as funções do Azure e o Azure Cosmos DB

[A Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é uma ótima maneira de armazenar dados não estruturados e JSON. Combinado com funções do Azure, o Cosmos DB torna o armazenamento de dados rápido e fácil, sendo necessário menos códigos para armazenar dados numa base de dados relacional.

> [!NOTE]
> Neste momento, o acionador do Azure Cosmos DB, os enlaces de entrada e os enlaces de saída funcionam apenas com contas de API do SQL e do Graph API.

Nas Funções do Azure, os enlaces de entrada e saída proporcionam uma forma declarativa para ligar aos dados do serviço externo a partir da sua função. Neste artigo, saiba como atualizar uma função existente para adicionar um enlace de saída que armazena dados não estruturados num documento do Azure Cosmos DB.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Tem de ter uma conta do Azure Cosmos DB que utilize a API SQL antes de criar o enlace de saída.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

1. No portal Azure, navegue e selecione a aplicação de função que criou anteriormente.

1. Selecione **Funções** e, em seguida, selecione a função HttpTrigger.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="Selecione a sua função Http no portal Azure." border="true":::

1. Selecione **Integração** e **+ Adicionar saída**.

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="Adicione uma ligação de saída Azure Cosmos DB." border="true":::

1. Utilize as definições **de Saída criar** conforme especificado na tabela:

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="Configurar a vinculação de saída da Azure Cosmos DB." border="true":::

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Tipo de Encadernação** | Azure Cosmos DB | Nome do tipo de ligação para selecionar para criar a ligação de saída ao Azure Cosmos DB. |
    | **Nome do parâmetro do documento** | taskDocument | Nome que se refere ao objeto do Cosmos DB no código. |
    | **Nome da base de dados** | taskDatabase | Nome da base de dados para guardar os documentos. |
    | **Nome da coleção** | tarefaCollecto | Nome da coleção da base de dados. |
    | **Se o valor for verdadeiro, cria a coleção e a base de dados do Cosmos DB** | Yes | A coleção ainda não existe, como tal, deve ser criada. |
    | **Ligação à conta do Cosmos DB** | Nova definição | Selecione **Novo**, em seguida, escolha **Azure Cosmos DB Account** e a **conta Base de Dados** que criou anteriormente e, em seguida, selecione **OK**. Cria uma definição da aplicação para a sua ligação de conta. Esta definição é utilizada pelo enlace para a ligação à base de dados. |

1. Selecione **OK** para criar a ligação.

## <a name="update-the-function-code"></a>Atualizar o código da função

Substitua o código da função existente pelo seguinte código no idioma à sua escolha:

# <a name="c"></a>[C#](#tab/csharp)

Substitua a função C# existente pelo seguinte código:

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Substitua a função JavaScript existente pelo seguinte código:

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

Este exemplo de código lê as cadeias de consulta do Pedido de HTTP e atribui-as a campos no objeto `taskDocument`. O enlace `taskDocument` envia os dados do objeto deste parâmetro de enlace para serem armazenados na base de dados de documentos vinculada. A base de dados é criada quando a função for executada pela primeira vez.

## <a name="test-the-function-and-database"></a>Testar a função e a base de dados

1. Selecione **Testar/Executar**. Em **Consulta**, selecione **+ Adicione o parâmetro** e adicione os seguintes parâmetros à cadeia de consulta:

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="Teste a função." border="true":::


1. Selecione **Executar** e verifique se um estado de 200 é devolvido.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="A screenshot mostra o estado do código de resposta HTTP 200 realçado após a seleção do Run." border="true":::


1. No portal Azure, procure e selecione **Azure Cosmos DB**.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="Procure o serviço Cosmos DB." border="true":::

1. Escolha a sua conta DB Azure Cosmos e, em seguida, selecione  **Data Explorer**.

1. Expanda os nós **TaskCollection,** selecione o novo documento e confirme que o documento contém os valores da sua cadeia de consulta, juntamente com alguns metadados adicionais.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="Verifique os valores das cordas no seu documento." border="true":::

Adicionou um enlace com êxito ao acionador de HTTP para armazenar os dados não estruturados no Azure Cosmos DB.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o enlace a uma base de dados do Cosmos DB, veja [Enlaces do Cosmos DB das Funções do Azure](functions-bindings-cosmosdb.md).

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
