---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: f4ba467b6d80c9ccafba0a91c1f04152b92cf869
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125160"
---
1. No Mac, visite o [Portal do Azure]. Clique em **Todos os Serviços** > **Serviço de Aplicações** > o back-end que acabou de criar. Nas definições da aplicação móvel, selecione o idioma que prefere:

   - Objective-C &ndash; **Início Rápido** > **iOS (Objective-C)**
   - SWIFT &ndash; **Início Rápido** > **iOS (Swift)**

     Em **3. Configurar a aplicação cliente**, clique em **Transferir**. Esta ação transfere um projeto Xcode concluído pré-configurado para ligar ao back-end. Abra o projeto com o Xcode.

1. Prima o botão **Executar** para criar o projeto e iniciar a aplicação no simulador do iOS.

1. Na aplicação, clique no sinal de adição (**+**) ícone, digite um texto significativo, tal como *concluir o tutorial*e, em seguida, clique em salvar botão. Esta ação envia um pedido POST ao back-end do Azure que implementou anteriormente. Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem SQL e devolve informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista.

   ![Aplicação de início rápido em execução no iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portal do Azure]: https://portal.azure.com/
