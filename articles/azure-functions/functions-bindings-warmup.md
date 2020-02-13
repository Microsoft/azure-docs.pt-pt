---
title: Azure Functions gatilho aquecimento
description: Entenda como usar o gatilho aquecimento no Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: Azure functions, functions, processamento de eventos, aquecimento, Cold Start, Premium, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167319"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions gatilho de aquecimento

Este artigo explica como trabalhar com o gatilho aquecimento no Azure Functions. O gatilho de aquecimento é suportado apenas para aplicações de função que funcionam num [plano Premium](functions-premium-plan.md). Um gatilho aquecimento é invocado quando uma instância é adicionada para dimensionar um aplicativo de funções em execução. Pode utilizar um gatilho de aquecimento para pré-carregar dependências personalizadas durante o [processo de pré-aquecimento](./functions-premium-plan.md#pre-warmed-instances) para que as suas funções estejam prontas para iniciar imediatamente o processamento de pedidos. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

É necessário o pacote [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versão **3.0.5 ou superior.** O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Acionador

O gatilho aquecimento permite que você defina uma função que será executada em uma nova instância quando ela for adicionada ao seu aplicativo em execução. Você pode usar uma função aquecimento para abrir conexões, carregar dependências ou executar qualquer outra lógica personalizada antes que seu aplicativo comece a receber tráfego. 

O gatilho aquecimento destina-se a criar dependências compartilhadas que serão usadas por outras funções em seu aplicativo. [Veja aqui exemplos de dependências partilhadas.](./manage-connections.md#client-code-examples)

Observe que o gatilho aquecimento é chamado apenas durante operações de expansão, não durante reinicializações ou outras inicializações sem escala. Você deve garantir que sua lógica possa carregar todas as dependências necessárias sem usar o gatilho aquecimento. O carregamento lento é um bom padrão para conseguir isso.

## <a name="trigger---example"></a>Acionador - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que será executada em cada nova instância quando for adicionada à sua aplicação. Um atributo de valor de retorno não é necessário.


* A sua função deve ser nomeada ```warmup``` (insensível a caso) e só pode haver uma função de aquecimento por app.
* Para utilizar o aquecimento como função de biblioteca de classe .NET, certifique-se de que tem uma referência de pacote a **Microsoft.Azure.WebJobs.Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Os comentários de espaço reservado mostram onde o aplicativo deve declarar e inicializar dependências compartilhadas. 
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
# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)


O exemplo seguinte mostra um gatilho de aquecimento num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que será executada em cada nova instância quando for adicionado à sua aplicação.

A sua função deve ser denominada ```warmup``` (insensível a casos), podendo haver apenas uma função de aquecimento por app.

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

Aqui está C# o código de script que se liga a `HttpRequest`:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Eis o código JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[python](#tab/python)

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

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo que se segue mostra um gatilho de aquecimento que funciona quando cada nova instância é adicionada à sua aplicação.

A sua função deve ser nomeada `warmup` (insensível a caso) e só pode haver uma função de aquecimento por app.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Acionador - atributos

Nas bibliotecas de [ C# turma,](functions-dotnet-class-library.md)o atributo `WarmupTrigger` está disponível para configurar a função.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Este exemplo demonstra como usar o atributo de [aquecimento.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)

Note que a sua função deve ser chamada ```Warmup``` e só pode haver uma função de aquecimento por app.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Para um exemplo completo, consulte o exemplo do [gatilho](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O gatilho aquecimento não tem suporte em Java como um atributo.

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `WarmupTrigger`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/d| Obrigatório - deve ser definido para `warmupTrigger`. |
| **direção** | n/d| Obrigatório - deve ser definido para `in`. |
| **nome** | n/d| Required – o nome da variável usada no código de função.|

## <a name="trigger---usage"></a>Acionador - utilização

Nenhuma informação adicional é fornecida a uma função disparada por aquecimento quando ela é chamada.

## <a name="trigger---limits"></a>Gatilho - limites

* O gatilho de aquecimento só está disponível para aplicações em execução no [plano Premium.](./functions-premium-plan.md)
* O gatilho aquecimento é chamado somente durante operações de escala vertical, não durante reinicializações ou outras inicializações sem escala. Você deve garantir que sua lógica possa carregar todas as dependências necessárias sem usar o gatilho aquecimento. O carregamento lento é um bom padrão para conseguir isso.
* O gatilho aquecimento não pode ser invocado depois que uma instância já está em execução.
* Só pode haver uma função de gatilho aquecimento por aplicativo de função.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)
