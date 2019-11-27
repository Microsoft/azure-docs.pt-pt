---
title: Exemplo de gatilho e Associação de Azure Functions
description: Saiba como configurar associações de função do Azure
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227247"
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

O primeiro elemento na matriz de `bindings` é o gatilho de armazenamento de fila. As propriedades `type` e `direction` identificam o gatilho. A propriedade `name` identifica o parâmetro de função que recebe o conteúdo da mensagem de fila. O nome da fila a ser monitorada está em `queueName`e a cadeia de conexão está na configuração do aplicativo identificada por `connection`.

O segundo elemento na matriz de `bindings` é a associação de saída do armazenamento de tabelas do Azure. As propriedades `type` e `direction` identificam a associação. A propriedade `name` especifica como a função fornece a nova linha da tabela, nesse caso, usando o valor de retorno da função. O nome da tabela está em `tableName`e a cadeia de conexão está na configuração do aplicativo identificada por `connection`.

Para exibir e editar o conteúdo de *Function. JSON* no portal do Azure, clique na opção **Editor avançado** na guia **integrar** da sua função.

> [!NOTE]
> O valor de `connection` é o nome de uma configuração de aplicativo que contém a cadeia de conexão, não a própria cadeia de conexão. As associações usam cadeias de conexão armazenadas nas configurações do aplicativo para impor a prática recomendada que o *Function. JSON* não contém segredos de serviço.

## <a name="c-script-example"></a>C#exemplo de script

Aqui está C# o código de script que funciona com esse gatilho e associação. Observe que o nome do parâmetro que fornece o conteúdo da mensagem da fila é `order`; Esse nome é necessário porque o valor da propriedade `name` em *Function. JSON* é `order` 

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

Em uma biblioteca de classes, as mesmas informações de gatilho e de associação &mdash; nomes de fila e tabela, contas de armazenamento, parâmetros de função para &mdash; de entrada e saída são fornecidas por atributos em vez de um arquivo function. JSON. Segue-se um exemplo:

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Padrões de expressão de associação de Azure Functions](./functions-bindings-expressions-patterns.md)
