---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673354"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Adicione uma definição de ligação de saída à função

Embora uma função possa ter apenas um gatilho, pode ter múltiplas ligações de entrada e saída, que permitem ligar-se a outros serviços e recursos Azure sem escrever código de integração personalizado. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Declara estas encadernações no *function.jsarquivado* na sua pasta de funções. A partir do início rápido anterior, o seu *function.jsficheiro na* pasta *HttpExample* contém duas encadernações na `bindings` coleção:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Cada encadernação tem pelo menos um tipo, uma direção e um nome. No exemplo acima, a primeira ligação é de tipo `httpTrigger` com a direção `in` . Para a `in` direção, `name` especifica o nome de um parâmetro de entrada que é enviado para a função quando invocado pelo gatilho.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
A segunda encadernação da coleção é `res` nomeada. Esta `http` ligação é uma ligação de saída `out` () que é usada para escrever a resposta HTTP. 

Para escrever para uma fila de armazenamento Azure a partir desta função, adicione uma `out` ligação do tipo `queue` com o nome , como mostrado no código `msg` abaixo:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
A segunda ligação na coleção é de tipo `http` com a `out` direção, caso em que o especial indica que esta `name` `$return` ligação utiliza o valor de retorno da função em vez de fornecer um parâmetro de entrada.

Para escrever para uma fila de armazenamento Azure a partir desta função, adicione uma `out` ligação do tipo `queue` com o nome , como mostrado no código `msg` abaixo:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
A segunda encadernação da coleção é `res` nomeada. Esta `http` ligação é uma ligação de saída `out` () que é usada para escrever a resposta HTTP. 

Para escrever para uma fila de armazenamento Azure a partir desta função, adicione uma `out` ligação do tipo `queue` com o nome , como mostrado no código `msg` abaixo:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Neste caso, `msg` é dado à função como argumento de saída. Para um `queue` tipo, deve também especificar o nome da fila `queueName` e fornecer o *nome* da ligação Azure Storage (a partir de *local.settings.jsem*) em `connection` . 
::: zone-end  
