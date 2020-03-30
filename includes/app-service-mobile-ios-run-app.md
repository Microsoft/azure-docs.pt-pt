---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240289"
---
1. Abra o projeto de cliente descarregado usando xcode.

2. Vá ao [portal Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Na `Overview` lâmina, procure o URL que é o ponto final público da sua aplicação móvel. Exemplo - o nome de site para o meu https://test123.azurewebsites.netnome de aplicação "test123" será .

3. Para o projeto Swift, abra o ficheiro `ToDoTableViewController.swift` nesta pasta - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. O nome `ZUMOAPPNAME`da aplicação é .

4. No `viewDidLoad()` método, `ZUMOAPPURL` substitua o parâmetro por ponto final público acima.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    torna-se
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Para o projeto Objective-C, abra o ficheiro `QSTodoService.m` nesta pasta - ZUMOAPPNAME/ZUMOAPPNAME. O nome `ZUMOAPPNAME`da aplicação é .

6. No `init` método, `ZUMOAPPURL` substitua o parâmetro por ponto final público acima.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    torna-se
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Prima o botão **Executar** para criar o projeto e iniciar a aplicação no simulador do iOS.

8. Na aplicação, clique**+** no ícone plus ( ) escreva texto significativo, como *completar o tutorial,* e, em seguida, clique no botão de guardar. Esta ação envia um pedido POST ao back-end do Azure que implementou anteriormente. Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem SQL e devolve informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista.

   ![Aplicação de início rápido em execução no iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
