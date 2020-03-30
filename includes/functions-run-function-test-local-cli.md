---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190880"
---
## <a name="run-the-function-locally"></a>Executar localmente a função

Executar a sua função iniciando o hospedeiro local funções Azure funcionando a partir da pasta *LocalFunctionProj:*

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

Perto do fim da saída, devem aparecer as seguintes linhas: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Se o HttpExample não aparecer como mostrado abaixo, provavelmente iniciou o hospedeiro a partir da pasta *HttpExample.* Nesse caso, utilize **o Ctrl**+**C** para parar o hospedeiro, navegue para a pasta *LocalFunctionProj* e volte a executar o comando anterior.

Copie o `HttpExample` URL da sua função desta saída para `?name=<your-name>`um browser e `http://localhost:7071/api/HttpExample?name=Functions`ateste a corda de consulta, fazendo com que o URL completo como . O navegador deve apresentar `Hello Functions`uma mensagem como:

![Resultado da função executada localmente no navegador](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

O terminal em `func start` que executou também mostra saída de log à medida que faz pedidos.

Quando estiver pronto, utilize **ctrl**+ `y` **C** e opte por parar o hospedeiro das funções.