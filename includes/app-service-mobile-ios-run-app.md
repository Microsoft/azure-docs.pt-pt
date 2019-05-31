---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240289"
---
1. Abra o projeto de cliente transferido com o Xcode.

2. Vá para o [portal do Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Sobre o `Overview` painel, procure o URL que é o ponto final público para a sua aplicação móvel. Exemplo - sitename para meu nome de aplicação "test123" será https://test123.azurewebsites.net.

3. Para o projeto Swift, abra o ficheiro `ToDoTableViewController.swift` nesta pasta - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. O nome da aplicação é `ZUMOAPPNAME`.

4. Na `viewDidLoad()` método, substitua `ZUMOAPPURL` parâmetro com o ponto final público acima.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    torna-se
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Para o projeto de Objective-C, abra o ficheiro `QSTodoService.m` nesta pasta - ZUMOAPPNAME/ZUMOAPPNAME. O nome da aplicação é `ZUMOAPPNAME`.

6. Na `init` método, substitua `ZUMOAPPURL` parâmetro com o ponto final público acima.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    torna-se
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Prima o botão **Executar** para criar o projeto e iniciar a aplicação no simulador do iOS.

8. Na aplicação, clique no sinal de adição ( **+** ) ícone, digite um texto significativo, tal como *concluir o tutorial*e, em seguida, clique em salvar botão. Esta ação envia um pedido POST ao back-end do Azure que implementou anteriormente. Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem SQL e devolve informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista.

   ![Aplicação de início rápido em execução no iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
