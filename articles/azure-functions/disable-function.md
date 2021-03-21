---
title: Como desativar funções em Funções Azure
description: Saiba como desativar e ativar funções em Funções Azure.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1ad484804f66a2e2d4d0f1da4a37cf0d6c485f38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584742"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desativar funções em Funções Azure

Este artigo explica como desativar uma função em Funções Azure. *Desativar* uma função significa fazer com que o tempo de execução ignore o gatilho automático definido para a função. Isto permite evitar que uma função específica funcione sem parar toda a aplicação de funções.

A forma recomendada de desativar uma função é com uma definição de aplicação no formato `AzureWebJobs.<FUNCTION_NAME>.Disabled` definido para `true` . Pode criar e modificar esta definição de aplicação de várias formas, incluindo utilizando o [CLI Azure](/cli/azure/) e a partir do **separador Visão Geral** da sua função no [portal Azure](https://portal.azure.com). 

> [!NOTE]  
> Quando desativa uma função HTTP ativada utilizando os métodos descritos neste artigo, o ponto final pode ainda ser acessível quando estiver a ser utilizado no computador local.  

## <a name="disable-a-function"></a>Desativar uma função

# <a name="portal"></a>[Portal](#tab/portal)

Utilize os botões **Ativar** e **Desativar** na página **'Visão Geral'** da função. Estes botões funcionam alterando o valor da configuração da `AzureWebJobs.<FUNCTION_NAME>.Disabled` aplicação. Esta definição específica da função é criada na primeira vez que é desativada. 

![Interruptor de estado de função](media/disable-function/function-state-switch.png)

Mesmo quando publica na sua aplicação de função a partir de um projeto local, ainda pode utilizar o portal para desativar funções na aplicação de função. 

> [!NOTE]  
> A funcionalidade de teste integrada no portal ignora a `Disabled` definição. Isto significa que uma função desativada ainda funciona quando iniciada a partir da janela **de Teste** no portal. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

No CLI Azure, utiliza o [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando para criar e modificar a configuração da aplicação. O seguinte comando desativa uma função nomeada `QueueTrigger` criando uma definição de aplicação com o nome `AzureWebJobs.QueueTrigger.Disabled` e definindo-a para `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Para voltar a ativar a função, repercuta o mesmo comando com um valor de `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

O [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) comando adiciona ou atualiza uma definição de aplicação. O seguinte comando desativa uma função nomeada `QueueTrigger` criando uma definição de aplicação com o nome `AzureWebJobs.QueueTrigger.Disabled` e definindo-a para `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Para voltar a ativar a função, repercuta o mesmo comando com um valor de `false` .

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Funções numa ranhura

Por predefinição, as definições de aplicações também se aplicam a aplicações em execução em slots de implementação. No entanto, pode anular a definição de aplicação utilizada pela ranhura, definindo uma definição de aplicação específica para slot. Por exemplo, pode querer que uma função seja ativa na produção, mas não durante os testes de implantação, como uma função de temporizador ativa. 

Para desativar uma função apenas na ranhura de preparação:

# <a name="portal"></a>[Portal](#tab/portal)

Navegue para a instância de slot da sua aplicação de função selecionando **slots de implementação** em **Implementação**, escolhendo a sua ranhura e selecionando **Funções** na instância de slot.  Escolha a sua função e, em seguida, utilize os botões **Ativar** e **Desativar** na página **'Visão Geral'** da função. Estes botões funcionam alterando o valor da configuração da `AzureWebJobs.<FUNCTION_NAME>.Disabled` aplicação. Esta definição específica da função é criada na primeira vez que é desativada. 

Também pode adicionar diretamente a definição de aplicação nomeada `AzureWebJobs.<FUNCTION_NAME>.Disabled` com valor da `true` **Configuração** para a instância de slot. Quando adicionar uma definição de aplicação específica para slot, certifique-se de verificar a caixa de definição de **ranhuras de implementação.** Isto mantém o valor de definição com a ranhura durante os swaps.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Para voltar a ativar a função, repercuta o mesmo comando com um valor de `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A Azure PowerShell não suporta atualmente esta funcionalidade.

---

Para saber mais, consulte [as ranhuras de implementação de funções Azure](functions-deployment-slots.md).

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
