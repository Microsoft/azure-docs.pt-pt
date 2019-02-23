---
title: Exemplo do Azure de Acionador e associação de funções
description: Saiba como configurar associações de função do Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 6d5f9b171a4efc5e52d281655de143ac9d40d437
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739740"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Exemplo do Azure de Acionador e associação de funções

Este artigo demonstra como configurar uma [acionador e os enlaces](./functions-triggers-bindings.md) numa função do Azure.

Suponha que queira escrever uma nova linha para o armazenamento de tabelas do Azure, sempre que uma nova mensagem aparece no armazenamento de filas do Azure. Este cenário pode ser implementado usando uma fila do Azure enlace de saída de Acionador do armazenamento e um armazenamento de tabelas do Azure. 

Aqui está uma *Function* ficheiros para este cenário. 

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

O primeiro elemento no `bindings` matriz é o acionador do armazenamento de fila. O `type` e `direction` propriedades identificar o acionador. O `name` propriedade identifica o parâmetro de função que recebe o conteúdo da mensagem de fila. O nome da fila para monitorizar está `queueName`, e a cadeia de ligação é na definição da aplicação identificada pelo `connection`.

O segundo elemento no `bindings` matriz é o armazenamento de tabelas do Azure enlace de saída. O `type` e `direction` propriedades identificar o enlace. O `name` propriedade especifica como a função fornece a nova linha de tabela, neste caso, utilizando a função devolver o valor. O nome da tabela está `tableName`, e a cadeia de ligação é na definição da aplicação identificada pelo `connection`.

Para ver e editar o conteúdo de *Function* no portal do Azure, clique nas **editor avançado** opção os **integrar** separador da sua função.

> [!NOTE]
> O valor de `connection` é o nome de uma definição de aplicação que contém a cadeia de ligação, não a cadeia de ligação em si. Enlaces de ligação de utilizar cadeias de caracteres nas definições da aplicação para impor a melhor prática que *Function* não contém segredos de serviço.

## <a name="c-script-example"></a>Exemplo de script do c#

Aqui está o script código c# que funciona com este acionador e a associação. Tenha em atenção que o nome do parâmetro que fornece o conteúdo da mensagem de fila é `order`; este nome é necessário porque o `name` valor da propriedade nas *Function* é `order` 

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

O mesmo *Function* ficheiro pode ser utilizado com uma função de JavaScript:

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

## <a name="class-library-example"></a>Exemplo de biblioteca de classe

Numa biblioteca de classes, o mesmo acionador e informações de enlace &mdash; parâmetros de entrada e saída de função de nomes de tabela e fila, contas de armazenamento, &mdash; é fornecido por atributos em vez de um ficheiro de Function. Segue-se um exemplo:

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

Agora tem uma função de trabalho que é acionada pelo armazenamento de tabelas do Azure que produz dados para uma fila.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Funções do Azure, padrões de expressões de enlace](./functions-bindings-expressions-patterns.md)