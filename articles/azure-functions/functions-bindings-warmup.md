---
title: Azure Functions trigger de aquecimento
description: Compreenda como utilizar o gatilho de aquecimento em Funções Azure.
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: funções, funções, processamento de eventos, aquecimento, arranque a frio, premium, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/08/2019
ms.author: cshoe
ms.openlocfilehash: f5523c513cc0bdd08c43bdbed5046bf662f1a3e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206573"
---
# <a name="azure-functions-warm-up-trigger"></a>Gatilho de aquecimento de funções Azure Functions

Este artigo explica como trabalhar com o gatilho de aquecimento em Funções Azure. O gatilho de aquecimento é suportado apenas para aplicações de função que funcionam num [plano Premium.](functions-premium-plan.md) Um gatilho de aquecimento é invocado quando um caso é adicionado para escalar uma aplicação de função de funcionamento. Pode utilizar um gatilho de aquecimento para pré-carregar dependências personalizadas durante o [processo de pré-aquecimento](./functions-premium-plan.md#pre-warmed-instances) para que as suas funções estejam prontas para iniciar os pedidos de processamento imediatamente. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superior

É necessário o pacote [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versão **3.0.5 ou superior.** O código fonte para o pacote está no [repositório GitHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Acionador

O gatilho de aquecimento permite definir uma função que será executada numa nova instância quando for adicionada à sua aplicação de execução. Pode utilizar uma função de aquecimento para abrir ligações, carregar dependências ou executar qualquer outra lógica personalizada antes que a sua aplicação comece a receber tráfego. 

O gatilho de aquecimento destina-se a criar dependências partilhadas que serão utilizadas pelas outras funções da sua aplicação. [Veja exemplos de dependências partilhadas aqui.](./manage-connections.md#client-code-examples)

Note que o gatilho de aquecimento só é chamado durante as operações de escala, não durante o recomeço ou outras startups não dimensionais. Deve certificar-se de que a sua lógica pode carregar todas as dependências necessárias sem utilizar o gatilho de aquecimento. Carregar preguiçoso é um bom padrão para conseguir isto.

## <a name="trigger---example"></a>Gatilho - exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que será executada em cada nova instância quando é adicionada à sua aplicação. Não é necessário um atributo de valor de retorno.


* A sua função deve ser nomeada ```warmup``` (caso-insensível) e só pode haver uma função de aquecimento por aplicação.
* Para utilizar o aquecimento como uma função de biblioteca de classe .NET, certifique-se de que tem uma referência de pacote a **Microsoft.Azure.WebJobs.Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Os comentários do espaço reservado mostram onde no pedido de declarar e inicializar dependências partilhadas. 
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


O exemplo a seguir mostra um gatilho de aquecimento numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que será executada em cada nova instância quando for adicionada à sua aplicação.

A sua função deve ser nomeada ```warmup``` (caso-insensível), e pode haver apenas uma função de aquecimento por aplicação.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#trigger---configuration) explica estas propriedades.

Aqui está o código de script C# que se liga `HttpRequest` a:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra um gatilho de aquecimento numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md)  que será executada em cada nova instância quando for adicionada à sua aplicação.

A sua função deve ser nomeada ```warmup``` (caso-insensível) e só pode haver uma função de aquecimento por aplicação.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#trigger---configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra um gatilho de aquecimento numa *function.jsno* ficheiro e uma [função Python](functions-reference-python.md) que será executada em cada nova instância quando for adicionada à sua aplicação.

A sua função deve ser nomeada ```warmup``` (caso-insensível) e só pode haver uma função de aquecimento por aplicação.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#trigger---configuration) explica estas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra um gatilho de aquecimento que é executado quando cada nova instância é adicionada à sua aplicação.

A sua função deve ser nomeada `warmup` (caso-insensível) e só pode haver uma função de aquecimento por aplicação.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Gatilho - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)o `WarmupTrigger` atributo está disponível para configurar a função.

# <a name="c"></a>[C#](#tab/csharp)

Este exemplo demonstra como utilizar o atributo [de aquecimento.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)

Note que a sua função deve ser chamada ```Warmup``` e só pode haver uma função de aquecimento por aplicação.

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

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

O gatilho de aquecimento não é suportado em Java como um atributo.

---

## <a name="trigger---configuration"></a>Gatilho - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `WarmupTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/a| Necessário - deve ser definido para `warmupTrigger` . |
| **direção** | n/a| Necessário - deve ser definido para `in` . |
| **nome** | n/a| Requerido - o nome variável utilizado no código de função.|

## <a name="trigger---usage"></a>Gatilho - utilização

Não são fornecidas informações adicionais a uma função ativada de aquecimento quando é invocada.

## <a name="trigger---limits"></a>Gatilho - limites

* O gatilho de aquecimento só está disponível para aplicações em execução no [plano Premium.](./functions-premium-plan.md)
* O gatilho de aquecimento só é chamado durante as operações de escala, não durante o recomeço ou outras startups não dimensionais. Deve certificar-se de que a sua lógica pode carregar todas as dependências necessárias sem utilizar o gatilho de aquecimento. Carregar preguiçoso é um bom padrão para conseguir isto.
* O gatilho de aquecimento não pode ser invocado uma vez que um caso já esteja em execução.
* Só pode haver uma função de gatilho de aquecimento por aplicação de função.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre as funções Azure desencadeia e encaderna](functions-triggers-bindings.md)
