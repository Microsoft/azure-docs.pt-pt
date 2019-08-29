---
title: Exemplo de gatilho e Associação de Azure Functions
description: Saiba como configurar associações de função do Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d1959792823e04cf34d65ab775ae8c51e741e293
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097476"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Exemplo de gatilho e Associação de Azure Functions

Este artigo demonstra como configurar um [gatilho e associações](./functions-triggers-bindings.md) em uma função do Azure.

Suponha que você deseja gravar uma nova linha no armazenamento de tabelas do Azure sempre que uma nova mensagem aparecer no armazenamento de filas do Azure. Esse cenário pode ser implementado usando um gatilho de armazenamento de filas do Azure e uma associação de saída do armazenamento de tabelas do Azure. 

Aqui está um arquivo *Function. JSON* para esse cenário. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

O primeiro elemento na `bindings` matriz é o gatilho de armazenamento de fila. As `type` propriedades `direction` e identificam o gatilho. A `name` propriedade identifica o parâmetro de função que recebe o conteúdo da mensagem de fila. O nome da fila a ser monitorada está `queueName`, e a cadeia de conexão está na configuração do aplicativo identificada por. `connection`

O segundo elemento na `bindings` matriz é a associação de saída do armazenamento de tabelas do Azure. As `type` propriedades `direction` e identificam a associação. A `name` propriedade especifica como a função fornece a nova linha da tabela, nesse caso, usando o valor de retorno da função. O nome da tabela está em `tableName`e a cadeia de conexão está na configuração do aplicativo identificada por. `connection`

Para exibir e editar o conteúdo de *Function. JSON* no portal do Azure, clique na opção **Editor avançado** na guia **integrar** da sua função.

> [!NOTE]
> O valor de `connection` é o nome de uma configuração de aplicativo que contém a cadeia de conexão, não a própria cadeia de conexão. As associações usam cadeias de conexão armazenadas nas configurações do aplicativo para impor a prática recomendada que o *Function. JSON* não contém segredos de serviço.

## <a name="c-script-example"></a>C#exemplo de script

Aqui está C# o código de script que funciona com esse gatilho e associação. Observe que o nome do parâmetro que fornece o conteúdo da mensagem da fila `order`é; esse nome é necessário porque `name` o valor da propriedade em *Function. JSON* é`order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Exemplo de JavaScript

O mesmo arquivo *Function. JSON* pode ser usado com uma função JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Exemplo de biblioteca de classes

Em uma biblioteca de classes, a mesma fila de informações &mdash; de gatilho e associação e nomes de tabela, contas de armazenamento, parâmetros &mdash; de função para entrada e saída são fornecidos por atributos em vez de um arquivo function. JSON. Segue-se um exemplo:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Agora você tem uma função funcional que é disparada por uma fila do Azure e gera dados para o armazenamento de tabelas do Azure.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Padrões de expressão de associação de Azure Functions](./functions-bindings-expressions-patterns.md)
