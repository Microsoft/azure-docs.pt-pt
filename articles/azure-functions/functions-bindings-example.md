---
title: Funções Azure gatilho e exemplo de ligação
description: Aprenda a configurar encadernações da Função Azure
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74227247"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Funções Azure gatilho e exemplo de ligação

Este artigo demonstra como configurar um [gatilho e encadernações](./functions-triggers-bindings.md) numa Função Azure.

Suponha que queira escrever uma nova linha para o armazenamento da Mesa Azure sempre que uma nova mensagem aparece no armazenamento da Fila Azure. Este cenário pode ser implementado com um acionador do armazenamento de Filas do Azure e um enlace de saída do armazenamento de Tabelas do Azure. 

Aqui está um ficheiro *função.json* para este cenário. 

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

O primeiro elemento `bindings` da matriz é o gatilho de armazenamento da fila. As `type` `direction` propriedades identificam o gatilho. A `name` propriedade identifica o parâmetro de função que recebe o conteúdo da mensagem de fila. O nome da fila para `queueName`monitorizar está dentro , e a `connection`cadeia de ligação está na definição da aplicação identificada por .

O segundo elemento `bindings` da matriz é a ligação de saída de armazenamento de mesa azure. As `type` `direction` propriedades identificam a ligação. A `name` propriedade especifica como a função fornece a nova linha de tabela, neste caso utilizando o valor de retorno da função. O nome da tabela `tableName`está em , e a cadeia `connection`de ligação está na definição da aplicação identificada por .

Para visualizar e editar o conteúdo do *function.json* no portal Azure, clique na opção **de editor Avançado** no separador **Integrado** da sua função.

> [!NOTE]
> O valor `connection` é o nome de uma definição de aplicação que contém a cadeia de ligação, e não a própria cadeia de ligação. As ligações utilizam cordas de ligação armazenadas nas definições da aplicação para impor as melhores práticas que *funcionam.json* não contém segredos de serviço.

## <a name="c-script-example"></a>Exemplo de roteiro C#

Aqui está o código de script C# que funciona com este gatilho e encadernação. Note que o nome do parâmetro que fornece `order`o conteúdo da mensagem de fila é; este nome é necessário `name` porque o valor de propriedade em *função.json* é`order` 

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

## <a name="javascript-example"></a>Exemplo javaScript

O mesmo *ficheiro função.json* pode ser usado com uma função JavaScript:

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

Numa biblioteca de classes, a &mdash; mesma fila de disparos e de informação vinculativa &mdash; e nomes de mesa, contas de armazenamento, parâmetros de função para entrada e saída são fornecidos por atributos em vez de um ficheiro function.json. Segue-se um exemplo:

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

Tem agora uma função de trabalho que é desencadeada por uma fila Azure e produz dados para armazenamento de Mesa Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Funções Azure padrões de expressão de ligação](./functions-bindings-expressions-patterns.md)
