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
ms.openlocfilehash: 108294e3f125da9fb009eb0a85585dab026c8d01
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933324"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions gatilho de aquecimento

Este artigo explica como trabalhar com o gatilho aquecimento no Azure Functions. O gatilho aquecimento tem suporte apenas para aplicativos de funções em execução em um [plano Premium](functions-premium-plan.md). Um gatilho aquecimento é invocado quando uma instância é adicionada para dimensionar um aplicativo de funções em execução. Você pode usar um gatilho aquecimento para pré-carregar dependências personalizadas durante o [processo de pré-teste](./functions-premium-plan.md#pre-warmed-instances) para que suas funções estejam prontas para iniciar solicitações de processamento imediatamente. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

O pacote NuGet [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) , versão **3.0.5 ou superior** , é necessário. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) . 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Acionador

O gatilho aquecimento permite que você defina uma função que será executada em uma nova instância quando ela for adicionada ao seu aplicativo em execução. Você pode usar uma função aquecimento para abrir conexões, carregar dependências ou executar qualquer outra lógica personalizada antes que seu aplicativo comece a receber tráfego. 

O gatilho aquecimento destina-se a criar dependências compartilhadas que serão usadas por outras funções em seu aplicativo. [Veja exemplos de dependências compartilhadas aqui](./manage-connections.md#client-code-examples).

Observe que o gatilho aquecimento é chamado apenas durante operações de expansão, não durante reinicializações ou outras inicializações sem escala. Você deve garantir que sua lógica possa carregar todas as dependências necessárias sem usar o gatilho aquecimento. O carregamento lento é um bom padrão para conseguir isso.

## <a name="trigger---example"></a>Acionador - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que será executada em cada nova instância quando ela for adicionada ao seu aplicativo. Um atributo de valor de retorno não é necessário.


* Sua função deve ser nomeada ```warmup``` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.
* Para usar o aquecimento como uma função de biblioteca de classes .NET, verifique se você tem uma referência de pacote para **Microsoft. Azure. webjobs. Extensions > = 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Os comentários de espaço reservado mostram onde o aplicativo deve declarar e inicializar dependências compartilhadas. 
[Saiba mais sobre dependências compartilhadas aqui](./manage-connections.md#client-code-examples).

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
# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)


O exemplo a seguir mostra um gatilho aquecimento em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que será executada em cada nova instância quando ela for adicionada ao seu aplicativo.

Sua função deve ser nomeada ```warmup``` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.

Aqui está o *Function* ficheiro:

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

O [configuração](#trigger---configuration) seção explica essas propriedades.

Aqui está C# o código de script que se associa a `HttpRequest`:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra um gatilho aquecimento em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que será executada em cada nova instância quando ela for adicionada ao seu aplicativo.

Sua função deve ser nomeada ```warmup``` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.

Aqui está o *Function* ficheiro:

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

O [configuração](#trigger---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir mostra um gatilho aquecimento em um arquivo *Function. JSON* e uma [função do Python](functions-reference-python.md) que será executada em cada nova instância quando ela for adicionada ao seu aplicativo.

Sua função deve ser nomeada ```warmup``` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.

Aqui está o *Function* ficheiro:

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

O [configuração](#trigger---configuration) seção explica essas propriedades.

Este é o código Python:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo a seguir mostra um gatilho aquecimento em um arquivo *Function. JSON* e em [funções Java](functions-reference-java.md) que serão executadas em cada nova instância quando ela for adicionada ao seu aplicativo.

Sua função deve ser nomeada ```warmup``` (não diferencia maiúsculas de minúsculas) e pode haver apenas uma função aquecimento por aplicativo.

Aqui está o *Function* ficheiro:

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

Eis o código Java:

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Acionador - atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), o atributo `WarmupTrigger` está disponível para configurar a função.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Este exemplo demonstra como usar o atributo [aquecimento](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) .

Observe que sua função deve ser chamada ```Warmup``` e pode haver apenas uma função aquecimento por aplicativo.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Para obter um exemplo completo, consulte o [exemplo de gatilho](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O gatilho aquecimento não tem suporte em Java como um atributo.

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `WarmupTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/d| Obrigatório-deve ser definido como `warmupTrigger`. |
| **direção** | n/d| Obrigatório-deve ser definido como `in`. |
| **name** | n/d| Required – o nome da variável usada no código de função.|

## <a name="trigger---usage"></a>Acionador - utilização

Nenhuma informação adicional é fornecida a uma função disparada por aquecimento quando ela é chamada.

## <a name="trigger---limits"></a>Gatilho-limites

* O gatilho aquecimento só está disponível para aplicativos em execução no [plano Premium](./functions-premium-plan.md).
* O gatilho aquecimento é chamado somente durante operações de escala vertical, não durante reinicializações ou outras inicializações sem escala. Você deve garantir que sua lógica possa carregar todas as dependências necessárias sem usar o gatilho aquecimento. O carregamento lento é um bom padrão para conseguir isso.
* O gatilho aquecimento não pode ser invocado depois que uma instância já está em execução.
* Só pode haver uma função de gatilho aquecimento por aplicativo de função.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
