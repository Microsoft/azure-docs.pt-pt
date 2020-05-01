---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 749b733039e89421ac33ef76a11f3291b296e718
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673118"
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

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
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
> Se o HttpExample não aparecer como mostrado abaixo, provavelmente começou o hospedeiro de fora da pasta raiz do projeto. Nesse caso, utilize **o CTRL**+**C** para parar o hospedeiro, navegue até à pasta raiz do projeto e volte a executar o comando anterior.

Copie o `HttpExample` URL da sua função desta saída para `?name=<your-name>`um browser e `http://localhost:7071/api/HttpExample?name=Functions`ateste a corda de consulta, fazendo com que o URL completo como . O navegador deve apresentar `Hello Functions`uma mensagem como:

![Resultado da função executada localmente no navegador](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

O terminal em que iniciou o seu projeto também mostra a saída de log à medida que faz pedidos.

Quando estiver pronto, utilize **ctrl**+ `y` **C** e opte por parar o hospedeiro das funções.