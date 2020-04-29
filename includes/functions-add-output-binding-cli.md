---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673354"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Adicione uma definição de ligação de saída à função

Embora uma função possa ter apenas um gatilho, pode ter várias ligações de entrada e saída, que permitem ligar-se a outros serviços e recursos Azure sem escrever código de integração personalizado. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Declara estas encadernações no ficheiro *função.json* na pasta de funções. Desde o quickstart anterior, o ficheiro *function.json* na pasta `bindings` *HttpExample* contém duas ligações na coleção:  
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
Cada encadernação tem pelo menos um tipo, uma direção e um nome. No exemplo acima, a primeira `httpTrigger` ligação `in`é de tipo com a direção . Para `in` a `name` direção, especifica o nome de um parâmetro de entrada enviado para a função quando invocado pelo gatilho.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
A segunda ligação da `res`coleção chama-se . Esta `http` ligação é`out`uma ligação de saída ( ) que é usada para escrever a resposta HTTP. 

Para escrever a uma fila de armazenamento `out` azure `queue` a partir `msg`desta função, adicione uma ligação de tipo com o nome, como indicado no código abaixo:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
A segunda ligação na `http` coleção `out`é de tipo `name` com `$return` a direção, caso em que o especial indica que esta ligação utiliza o valor de retorno da função em vez de fornecer um parâmetro de entrada.

Para escrever a uma fila de armazenamento `out` azure `queue` a partir `msg`desta função, adicione uma ligação de tipo com o nome, como indicado no código abaixo:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
A segunda ligação da `res`coleção chama-se . Esta `http` ligação é`out`uma ligação de saída ( ) que é usada para escrever a resposta HTTP. 

Para escrever a uma fila de armazenamento `out` azure `queue` a partir `msg`desta função, adicione uma ligação de tipo com o nome, como indicado no código abaixo:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Neste caso, `msg` é dada à função como um argumento de saída. Para `queue` um tipo, deve também especificar o `queueName` nome da fila e fornecer o *nome* da ligação De `connection`armazenamento Azure (a partir de *local.settings.json*) em . 
::: zone-end  
