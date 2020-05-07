---
title: Gatilho de aquecimento funções azure
description: Compreenda como usar o gatilho de aquecimento nas Funções Azure.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: funções, funções azure, processamento de eventos, aquecimento, arranque a frio, premium, computação dinâmica, arquitetura sem servidores
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: 72a6f97f45ec1adaa42d1f17a1916af137845392
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559987"
---
# <a name="azure-functions-warm-up-trigger"></a>Gatilho de aquecimento funções azure

Este artigo explica como trabalhar com o gatilho de aquecimento em Funções Azure. O gatilho de aquecimento é suportado apenas para aplicações de função que funcionam num [plano Premium](functions-premium-plan.md). Um gatilho de aquecimento é invocado quando uma instância é adicionada para escalar uma aplicação de função de funcionamento. Pode utilizar um gatilho de aquecimento para pré-carregar dependências personalizadas durante o [processo de pré-aquecimento](./functions-premium-plan.md#pre-warmed-instances) para que as suas funções estejam prontas para iniciar imediatamente o processamento de pedidos. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

É necessário o pacote [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versão **3.0.5 ou superior.** O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Acionador

O gatilho de aquecimento permite definir uma função que será executada numa nova instância quando for adicionada à sua aplicação de execução. Pode utilizar uma função de aquecimento para abrir ligações, carregar dependências ou executar qualquer outra lógica personalizada antes que a sua aplicação comece a receber tráfego. 

O gatilho de aquecimento destina-se a criar dependências partilhadas que serão utilizadas pelas outras funções da sua aplicação. [Veja aqui exemplos de dependências partilhadas.](./manage-connections.md#client-code-examples)

Note que o gatilho de aquecimento só é chamado durante as operações de escala, não durante o reinício ou outras startups não à escala. Deve certificar-se de que a sua lógica pode carregar todas as dependências necessárias sem utilizar o gatilho de aquecimento. Carga preguiçosa é um bom padrão para conseguir isto.

## <a name="trigger---example"></a>Gatilho - exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que será executada em cada nova instância quando for adicionada à sua aplicação. Não é necessário um atributo de valor de retorno.


* A sua função deve ser nomeada ```warmup``` (insensível a caso) e só pode haver uma função de aquecimento por app.
* Para utilizar o aquecimento como função de biblioteca de classe .NET, certifique-se de que tem uma referência de pacote a **Microsoft.Azure.WebJobs.Extensões >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Os comentários do espaço reservado mostram onde na aplicação para declarar e inicializar dependências partilhadas. 
[Saiba mais sobre dependências partilhadas aqui.](./manage-connections.md#client-code-examples)

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[C# Script](#tab/csharp-script)


O exemplo seguinte mostra um gatilho de aquecimento num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que será executada em cada nova instância quando for adicionada à sua aplicação.

A sua função deve ser nomeada ```warmup``` (insensível a casos), podendo haver apenas uma função de aquecimento por app.

Aqui está o ficheiro *função.json:*

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A secção de [configuração](#trigger---configuration) explica estas propriedades.

Aqui está o código de script `HttpRequest`C# que se liga a:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra um gatilho de aquecimento num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que será executada em cada nova instância quando for adicionada à sua aplicação.

A sua função deve ser nomeada ```warmup``` (insensível a caso) e só pode haver uma função de aquecimento por app.

Aqui está o ficheiro *função.json:*

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A secção de [configuração](#trigger---configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo seguinte mostra um gatilho de aquecimento num ficheiro *function.json* e uma [função Python](functions-reference-python.md) que será executada em cada nova instância quando for adicionada à sua aplicação.

A sua função deve ser nomeada ```warmup``` (insensível a caso) e só pode haver uma função de aquecimento por app.

Aqui está o ficheiro *função.json:*

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A secção de [configuração](#trigger---configuration) explica estas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

O exemplo que se segue mostra um gatilho de aquecimento que funciona quando cada nova instância é adicionada à sua aplicação.

A sua função deve ser nomeada `warmup` (insensível a caso) e só pode haver uma função de aquecimento por app.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Gatilho - atributos

Nas bibliotecas da `WarmupTrigger` [classe C#,](functions-dotnet-class-library.md)o atributo está disponível para configurar a função.

# <a name="c"></a>[C #](#tab/csharp)

Este exemplo demonstra como usar o atributo de [aquecimento.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)

Note que a sua ```Warmup``` função deve ser chamada e só pode haver uma função de aquecimento por app.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Para um exemplo completo, consulte o exemplo do [gatilho](#trigger---example).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

O gatilho de aquecimento não é suportado em Java como um atributo.

---

## <a name="trigger---configuration"></a>Gatilho - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `WarmupTrigger` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/d| Obrigatório - deve ser `warmupTrigger`definido para . |
| **direção** | n/d| Obrigatório - deve ser `in`definido para . |
| **nome** | n/d| Obrigatório - o nome variável utilizado no código de função.|

## <a name="trigger---usage"></a>Gatilho - utilização

Não são fornecidas informações adicionais a uma função desencadeada pelo aquecimento quando é invocada.

## <a name="trigger---limits"></a>Gatilho - limites

* O gatilho de aquecimento só está disponível para aplicações em execução no [plano Premium.](./functions-premium-plan.md)
* O gatilho de aquecimento só é chamado durante as operações de escala, não durante o reinício ou outras startups não à escala. Deve certificar-se de que a sua lógica pode carregar todas as dependências necessárias sem utilizar o gatilho de aquecimento. Carga preguiçosa é um bom padrão para conseguir isto.
* O gatilho de aquecimento não pode ser invocado uma vez que uma instância já está em execução.
* Só pode haver uma função de gatilho de aquecimento por aplicação de função.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)
