---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949969"
---
## <a name="create-the-local-function-app-project"></a>Criar o projeto de aplicação de funções local

Execute o seguinte comando na linha de comandos para criar um projeto de aplicação de funções na pasta `MyFunctionProj` do diretório local atual. Também é criado um repositório do GitHub em `MyFunctionProj`.

```command
func init MyFunctionProj
```

Quando lhe for pedido, selecione um runtime de trabalho a partir das seguintes opções de linguagem:

+ `dotnet`: cria um projeto de biblioteca de classes .NET (.csproj).
+ `node`: cria um projeto baseado em node. js. `javascript` Escolha ou .`typescript` 
+ `python`: para um projeto Python, em vez disso, conclua [a criação de uma função disparada por http no Azure](../articles/azure-functions/functions-create-first-function-python.md).
+ `powershell`: para um projeto do PowerShell, em vez disso, conclua [criar sua primeira função do PowerShell no Azure](../articles/azure-functions/functions-create-first-function-powershell.md). 


Depois que o projeto for criado, use o comando a seguir para navegar até `MyFunctionProj` a nova pasta do projeto.

```command
cd MyFunctionProj
```
