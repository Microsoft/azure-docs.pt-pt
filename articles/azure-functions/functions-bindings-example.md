---
title: Exemplo de acionadores e de enlaces das Funções do Azure
description: Aprenda a configurar encadernações de funções Azure
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: c95524a5de6696bd0ffe7463451d152a9d3a19b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88205973"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Exemplo de acionadores e de enlaces das Funções do Azure

Este artigo demonstra como configurar um [gatilho e encadernações](./functions-triggers-bindings.md) numa Função Azure.

Suponha que queira escrever uma nova linha para o armazenamento da Mesa Azure sempre que uma nova mensagem aparece no armazenamento da Fila Azure. Este cenário pode ser implementado com um acionador do armazenamento de Filas do Azure e um enlace de saída do armazenamento de Tabelas do Azure. 

Aqui está uma *function.jspara* este cenário. 

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

O primeiro elemento da `bindings` matriz é o gatilho de armazenamento da fila. As `type` `direction` propriedades identificam o gatilho. A `name` propriedade identifica o parâmetro de função que recebe o conteúdo da mensagem de fila. O nome da fila para monitorar está dentro `queueName` , e a cadeia de ligação está na definição da aplicação identificada por `connection` .

O segundo elemento da matriz é a ligação de `bindings` saída de armazenamento de mesa Azure. As `type` propriedades e propriedades `direction` identificam a ligação. A `name` propriedade especifica como a função fornece a nova linha de tabela, neste caso usando o valor de retorno da função. O nome da tabela está em `tableName` , e a cadeia de ligação está na definição da aplicação identificada por `connection` .

Para visualizar e editar os conteúdos de *function.jsno* portal Azure, clique na opção **editor Avançado** no **separador Integração** da sua função.

> [!NOTE]
> O valor é `connection` o nome de uma definição de aplicação que contém a cadeia de ligação, não a própria cadeia de ligação. As ligações utilizam cadeias de ligação armazenadas nas definições de aplicações para impor as melhores práticas em que *function.js* não contém segredos de serviço.

## <a name="c-script-example"></a>Exemplo de script C#

Aqui está o código de script C# que funciona com este gatilho e ligação. Note que o nome do parâmetro que fornece o conteúdo da mensagem de fila é `order` ; este nome é necessário porque o valor da propriedade emfunction.js`name` *em* é `order` 

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

## <a name="javascript-example"></a>Exemplo JavaScript

O mesmo *function.jsno* ficheiro pode ser usado com uma função JavaScript:

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

Numa biblioteca de classes, o mesmo gatilho e ligação de nomes de informação &mdash; e nomes de tabelas, contas de armazenamento, parâmetros de função para entrada e saída &mdash; são fornecidos por atributos em vez de um function.jsno ficheiro. Eis um exemplo:

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

Tem agora uma função de funcionamento que é desencadeada por uma Fila Azure e produz dados para o armazenamento da Tabela Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Padrões de expressão de ligação Azure Functions](./functions-bindings-expressions-patterns.md)
