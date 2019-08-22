---
title: Como desabilitar funções no Azure Functions
description: Saiba como desabilitar e habilitar funções no Azure Functions 1. x e 2. x.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 498bb8c0f1e7bb674605d4a98f0be0f3e0b9a7c9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650484"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desabilitar funções no Azure Functions

Este artigo explica como desabilitar uma função no Azure Functions. Para *desabilitar* uma função significa fazer com que o tempo de execução ignore o gatilho automático definido para a função. A maneira como você faz isso depende da versão de tempo de execução e da linguagem de programação:

* Funções 2. x:
  * Uma maneira para todos os idiomas
  * Maneira opcional para C# bibliotecas de classes
* Funções 1. x:
  * Linguagens de script
  * C#bibliotecas de classes

## <a name="functions-2x---all-languages"></a>Funções 2. x-todos os idiomas

No functions 2. x, você desabilita uma função usando uma configuração de aplicativo no formato `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Você pode criar e modificar essa configuração de aplicativo de várias maneiras, incluindo usando o [CLI do Azure](/cli/azure/) e da guia **gerenciar** da função na [portal do Azure](https://portal.azure.com). 

### <a name="azure-cli"></a>CLI do Azure

No CLI do Azure, você usa o [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando para criar e modificar a configuração do aplicativo. O comando a seguir desabilita uma função denominada `QueueTrigger` criando uma configuração de aplicativo chamada `AzureWebJobs.QueueTrigger.Disabled` set to `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Para reabilitar a função, execute novamente o mesmo comando com um valor de `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portal

Você também pode usar a opção **estado de função** na guia **gerenciar** da função. A opção funciona criando e excluindo a `AzureWebJobs.<FUNCTION_NAME>.Disabled` configuração do aplicativo.

![Opção de estado de função](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Bibliotecas de classes 2. C# x de funções

Em uma biblioteca de classes do Functions 2. x, recomendamos que você use o método que funciona para todos os idiomas. Mas se preferir, você pode [usar o atributo disable como nas funções 1. x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Funções 1. x-linguagens de script

Para linguagens de script, C# como script e JavaScript, você usa `disabled` a propriedade do arquivo *Function. JSON* para informar ao tempo de execução para não disparar uma função. Essa propriedade pode ser definida como `true` ou para o nome de uma configuração de aplicativo:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
ou 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

No segundo exemplo, a função é desabilitada quando há uma configuração de aplicativo chamada IS_DISABLED e é definida como `true` ou 1.

Você pode editar o arquivo no portal do Azure ou usar a opção **estado da função** na guia **gerenciar** da função. O comutador do portal funciona alterando o arquivo *Function. JSON* .

![Opção de estado de função](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Bibliotecas de classes 1. C# x de funções

Em uma biblioteca de classes do Functions 1. x, `Disable` você usa um atributo para impedir que uma função seja disparada. Você pode usar o atributo sem um parâmetro de construtor, conforme mostrado no exemplo a seguir:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

O atributo sem um parâmetro de construtor requer que você recompile e reimplante o projeto para alterar o estado de desabilitado da função. Uma maneira mais flexível de usar o atributo é incluir um parâmetro de construtor que se refere a uma configuração de aplicativo booliana, conforme mostrado no exemplo a seguir:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Esse método permite habilitar e desabilitar a função alterando a configuração do aplicativo, sem recompilar ou reimplantar. A alteração de uma configuração de aplicativo faz com que o aplicativo de funções seja reiniciado, portanto, a alteração de Estado desabilitada é reconhecida imediatamente.

> [!IMPORTANT]
> O `Disabled` atributo é a única maneira de desabilitar uma função de biblioteca de classes. O arquivo *Function. JSON* gerado para uma função da biblioteca de classes não deve ser editado diretamente. Se você editar esse arquivo, o que for feito para `disabled` a propriedade não terá nenhum efeito.
>
> O mesmo vale para a opção de **estado de função** na guia **gerenciar** , pois ela funciona alterando o arquivo *Function. JSON* .
>
> Além disso, observe que o portal pode indicar que a função está desabilitada quando não está.

## <a name="next-steps"></a>Passos Seguintes

Este artigo é sobre a desabilitação de gatilhos automáticos. Para obter mais informações sobre gatilhos, consulte [gatilhos e associações](functions-triggers-bindings.md).
