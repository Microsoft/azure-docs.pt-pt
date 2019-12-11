---
title: Como desabilitar funções no Azure Functions
description: Saiba como desabilitar e habilitar funções no Azure Functions.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: bffb3136c77074ecd50e839fd7c73144ad910967
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970980"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desabilitar funções no Azure Functions

Este artigo explica como desabilitar uma função no Azure Functions. Para *desabilitar* uma função significa fazer com que o tempo de execução ignore o gatilho automático definido para a função. Isso permite impedir que uma função específica seja executada sem interromper o aplicativo de funções inteiro.

A maneira recomendada para desabilitar uma função é usando uma configuração de aplicativo no formato `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Você pode criar e modificar essa configuração de aplicativo de várias maneiras, incluindo usando o [CLI do Azure](/cli/azure/) e da guia **gerenciar** da função na [portal do Azure](https://portal.azure.com). 

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

No CLI do Azure, use o comando [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) para criar e modificar a configuração do aplicativo. O comando a seguir desabilita uma função chamada `QueueTrigger` criando uma configuração de aplicativo chamada `AzureWebJobs.QueueTrigger.Disabled` defini-la como `true`. 

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

## <a name="use-the-portal"></a>Usar o portal

Você também pode usar a opção **estado de função** na guia **gerenciar** da função. A opção funciona criando e excluindo a configuração do aplicativo `AzureWebJobs.<FUNCTION_NAME>.Disabled`.

![Opção de estado de função](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Outros métodos

Embora o método de configuração de aplicativo seja recomendado para todas as linguagens e todas as versões de tempo de execução, há várias outras maneiras de desabilitar funções. Esses métodos, que variam por idioma e versão de tempo de execução, são mantidos para compatibilidade com versões anteriores. 

### <a name="c-class-libraries"></a>C#bibliotecas de classes

Em uma função de biblioteca de classes, você também pode usar o atributo `Disable` para impedir que a função seja disparada. Você pode usar o atributo sem um parâmetro de construtor, conforme mostrado no exemplo a seguir:

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
> O atributo `Disabled` é a única maneira de desabilitar uma função de biblioteca de classes. O arquivo *Function. JSON* gerado para uma função da biblioteca de classes não deve ser editado diretamente. Se você editar esse arquivo, o que for feito para a propriedade `disabled` não terá nenhum efeito.
>
> O mesmo vale para a opção de **estado de função** na guia **gerenciar** , pois ela funciona alterando o arquivo *Function. JSON* .
>
> Além disso, observe que o portal pode indicar que a função está desabilitada quando não está.

### <a name="functions-1x---scripting-languages"></a>Funções 1. x-linguagens de script

Na versão 1. x, você também pode usar a propriedade `disabled` do arquivo *Function. JSON* para informar ao tempo de execução para não disparar uma função. Esse método funciona apenas para linguagens de script, C# como script e JavaScript. A propriedade `disabled` pode ser definida como `true` ou como o nome de uma configuração de aplicativo:

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

## <a name="next-steps"></a>Passos seguintes

Este artigo é sobre a desabilitação de gatilhos automáticos. Para obter mais informações sobre gatilhos, consulte [gatilhos e associações](functions-triggers-bindings.md).
