---
title: Como desativar funções nas funções do Azure
description: Saiba como desativar e ativar as funções nas funções do Azure 1.x e 2.x.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
origin.date: 07/24/2018
ms.date: 08/31/2018
ms.author: v-junlch
ms.openlocfilehash: a32b4815a2716428ceeec034ddc5589e3aa062e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710580"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Como desativar funções nas funções do Azure

Este artigo explica como desativar uma função nas funções do Azure. Para *desativar* significa que uma função tornar o tempo de execução ignorar o acionador automática que está definido para a função. Fazemos isso depende da versão de runtime e a linguagem de programação:

- Funções 1.x
  - Linguagens de script
  - Bibliotecas de classes do c#
- Funções 2.x
  - Uma forma para todos os idiomas
  - Forma opcional para bibliotecas de classes do c#

## <a name="functions-1x---scripting-languages"></a>As funções 1.x - linguagens de script

Para linguagens de script, como o script c# e JavaScript, utilize o `disabled` propriedade o *Function* ficheiro para informar o tempo de execução não para acionar uma função. Esta propriedade pode ser definida como `true` ou para o nome de uma definição de aplicação:

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

No segundo exemplo, a função é desativada quando existe uma definição de aplicação que tem o nome IS_DISABLED e está definida como `true` ou 1.

Pode editar o ficheiro no portal do Azure ou utilize o **estado de função** mudar a função **gerir** separador. O comutador portal funciona, alterando a *Function* ficheiro.

![Comutador de estado de função](./media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>As funções 1.x - bibliotecas de classes do c#

Uma biblioteca de classes do funções 1.x, vai utilizar um `Disable` atributo para impedir que uma função que está a ser acionado. Pode usar o atributo sem um parâmetro de construtor, conforme mostrado no exemplo a seguir:

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

O atributo sem um parâmetro de construtor requer que recompila e reimplantar o projeto para alterar o estado de desativado da função. É uma maneira mais flexível para usar o atributo incluir um parâmetro de construtor que se refere a uma definição de aplicação booleano, conforme mostrado no exemplo a seguir:

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

Este método permite-lhe ativar e desativar a função ao alterar a definição de aplicação, sem recompilar ou voltar a implementar. Alterar uma definição de aplicação faz com que a aplicação de funções reiniciar, para que a alteração de estado desativado é reconhecida imediatamente.

> [!IMPORTANT]
> O `Disabled` atributo é a única forma de desativar uma função de biblioteca de classe. O gerado *Function* de ficheiros para uma função de biblioteca de classe não se destina a ser editada diretamente. Se editar esse arquivo, tudo o que fizer para o `disabled` propriedade não tem qualquer efeito.
>
> O mesmo vale para o **estado de função** alternar o **gerir** separador, uma vez que ele funciona, alterando o *Function* ficheiro.
>
> Além disso, tenha em atenção que o portal pode indicar que a função é desativada quando não é.



## <a name="functions-2x---all-languages"></a>As funções 2.x - todos os idiomas

Nas funções 2.x desativar uma função utilizando uma definição de aplicação. Por exemplo, para desativar uma função chamada `QueueTrigger`, cria uma definição de aplicação com o nome `AzureWebJobs.QueueTrigger.Disabled`e defina-o como `true`. Para ativar a função, definir a definição de aplicação `false`. Também pode utilizar o **estado de função** mudar a função **gerir** separador. O comutador funciona através da criação e a eliminar o `AzureWebJobs.<functionname>.Disabled` definição de aplicação.

![Comutador de estado de função](./media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>As funções 2.x - bibliotecas de classes do c#

Uma biblioteca de classes de 2.x de funções, recomendamos que utilize o método que funciona para todos os idiomas. Mas se preferir, pode [uso o atributo de desativar funções como em 1.x](#functions-1x---c-class-libraries).

## <a name="next-steps"></a>Passos Seguintes

Este artigo é sobre desabilitando disparadores automática. Para obter mais informações sobre os acionadores, veja [Acionadores e enlaces](functions-triggers-bindings.md).

