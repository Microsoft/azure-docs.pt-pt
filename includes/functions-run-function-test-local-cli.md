---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 11d426016cfe1a8a9ff843da518f57c08881be5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96842339"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

1. Executar a sua função iniciando o anfitrião local do tempo de execução Azure Functions a partir da pasta *LocalFunctionProj:*

    ```
    func start
    ```

    No final da saída, devem aparecer as seguintes linhas: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Se httpExample não aparecer como mostrado acima, é provável que tenha iniciado o hospedeiro de fora da pasta raiz do projeto. Nesse caso, utilize **o Ctrl** + **C** para parar o hospedeiro, navegue na pasta raiz do projeto e volte a executar o comando anterior.

1. Copie o URL da sua `HttpExample` função desta saída para um browser e apencha a cadeia de `?name=<YOUR_NAME>` consulta, fazendo com que o URL completo seja semelhante `http://localhost:7071/api/HttpExample?name=Functions` a . O navegador deve exibir uma mensagem `Hello Functions` como:

    ![Resultado da função executado localmente no navegador](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. O terminal em que iniciou o seu projeto também mostra a saída de registo à medida que faz pedidos.

1. Quando terminar, use **ctrl** + **C** e opte por `y` parar o hospedeiro de funções.
