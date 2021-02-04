---
title: Como desativar funções em Funções Azure
description: Saiba como desativar e ativar funções em Funções Azure.
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cbb84308507ea15f1c44c00122a9a59472f12a88
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551048"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desativar funções em Funções Azure

Este artigo explica como desativar uma função em Funções Azure. *Desativar* uma função significa fazer com que o tempo de execução ignore o gatilho automático definido para a função. Isto permite evitar que uma função específica funcione sem parar toda a aplicação de funções.

A forma recomendada de desativar uma função é com uma definição de aplicação no formato `AzureWebJobs.<FUNCTION_NAME>.Disabled` definido para `true` . Pode criar e modificar esta definição de aplicação de várias formas, incluindo utilizando o [CLI Azure](/cli/azure/) e a partir do **separador Visão Geral** da sua função no [portal Azure](https://portal.azure.com). 

> [!NOTE]  
> Quando desativa uma função HTTP ativada utilizando os métodos descritos neste artigo, o ponto final pode ainda ser acessível quando estiver a ser utilizado no computador local.  

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

No CLI Azure, utiliza o [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando para criar e modificar a configuração da aplicação. O seguinte comando desativa uma função nomeada `QueueTrigger` através da criação de uma definição de aplicação com `AzureWebJobs.QueueTrigger.Disabled` o nome de definição para `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Para voltar a ativar a função, repercuta o mesmo comando com um valor de `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Utilizar o Portal

Também pode utilizar os botões **Ativar** e **Desativar** na página **'Visão Geral'** da função. Estes botões funcionam alterando o valor da configuração da `AzureWebJobs.<FUNCTION_NAME>.Disabled` aplicação. Esta definição específica da função é criada na primeira vez que é desativada. 

![Interruptor de estado de função](media/disable-function/function-state-switch.png)

Mesmo quando publica na sua aplicação de função a partir de um projeto local, ainda pode utilizar o portal para desativar funções na aplicação de função. 

> [!NOTE]  
> A funcionalidade de teste integrada no portal ignora a `Disabled` definição. Isto significa que uma função desativada ainda funciona quando iniciada a partir da janela **de Teste** no portal. 

## <a name="localsettingsjson"></a>local.settings.json

As funções podem ser desativadas da mesma forma quando funcionam localmente. Para desativar uma função denominada `HttpExample` , adicione uma entrada à coleção Valores no local.settings.jsficheiro, da seguinte forma:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Outros métodos

Embora o método de definição de aplicação seja recomendado para todas as idiomas e todas as versões de tempo de execução, existem várias outras formas de desativar as funções. Estes métodos, que variam de acordo com a versão linguística e de tempo de execução, são mantidos para retrocompatibilidade. 

### <a name="c-class-libraries"></a>Bibliotecas de classe C#

Numa função de biblioteca de classes, também pode utilizar o `Disable` atributo para evitar que a função seja ativada. Este atributo permite personalizar o nome da definição utilizada para desativar a função. Utilize a versão do atributo que lhe permite definir um parâmetro de construtor que se refere a uma configuração de aplicação Boolean, como mostra o seguinte exemplo:

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

Este método permite ativar e desativar a função alterando a definição da aplicação, sem recompensá-la ou redistribuir. A alteração da definição de uma aplicação faz com que a aplicação de função reinicie, pelo que a alteração do estado desativada é imediatamente reconhecida.

Há também um construtor para o parâmetro que não aceita uma corda para o nome de definição. Esta versão do atributo não é recomendada. Se utilizar esta versão, deve recompiler e recolocar o projeto para alterar o estado desativado da função.

### <a name="functions-1x---scripting-languages"></a>Funções 1.x - Scripting languages

Na versão 1.x, também pode utilizar a `disabled` propriedade dofunction.js *no* ficheiro para dizer ao tempo de execução para não desencadear uma função. Este método funciona apenas para scripting linguagens como o script C# e o JavaScript. A `disabled` propriedade pode ser configurada para ou para o nome de uma `true` configuração de aplicação:

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

No segundo exemplo, a função é desativada quando há uma definição de aplicação que é nomeada IS_DISABLED e está definida para `true` ou 1.

>[!IMPORTANT]  
>O portal utiliza as definições de aplicação para desativar as funções v1.x. Quando uma aplicação que estabelece entra em conflito com o function.jsno ficheiro, pode ocorrer um erro. Deve retirar a `disabled` propriedade do function.jsficheiro para evitar erros. 


## <a name="next-steps"></a>Passos seguintes

Este artigo é sobre desativar os gatilhos automáticos. Para obter mais informações sobre os gatilhos, consulte [Triggers e encadernações](functions-triggers-bindings.md).
