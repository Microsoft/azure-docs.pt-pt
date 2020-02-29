---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
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
> Se o HttpExample não aparecer como mostrado abaixo, provavelmente iniciou o hospedeiro a partir da pasta *HttpExample.* Nesse caso, utilize **o Ctrl**+**C** para parar o hospedeiro, navegar para a pasta *LocalFunctionProj* e executar novamente o comando anterior.

Copie o URL da sua função `HttpExample` a partir desta saída para um browser e ateste a `?name=<your-name>`de cadeia seletiva, tornando o URL completo como `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve apresentar uma mensagem como `Hello Functions`:

![Resultado da função executada localmente no navegador](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

O terminal em que correu `func start` também mostra saída de log à medida que faz pedidos.

Quando estiver pronto, utilize **ctrl**+**C** e escolha `y` para parar o hospedeiro das funções.