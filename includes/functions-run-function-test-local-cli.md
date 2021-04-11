---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d400533039ea74a878cb8e543c22de02ee77e4f5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283170"
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

1. Copie o URL da sua `HttpExample` função desta saída para um browser e apencha a cadeia de `?name=<YOUR_NAME>` consulta, fazendo com que o URL completo seja semelhante `http://localhost:7071/api/HttpExample?name=Functions` a . O navegador deve apresentar uma mensagem de resposta que ecoe o valor da sua cadeia de consulta. O terminal em que iniciou o seu projeto também mostra a saída de registo à medida que faz pedidos.

1. Quando terminar, use **ctrl** + **C** e opte por `y` parar o hospedeiro de funções.
