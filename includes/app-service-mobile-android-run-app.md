---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 505eac0996129a17b6b68e8ab4ea2d4fc80fd473
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140992"
---
1. Visite o [Portal do Azure]. Clique em **Procurar Tudo** > **Mobile Apps** > o back-end que acabou de criar. Nas definições da aplicação móvel, clique em **Início Rápido** > **Android**. Em **Configurar a aplicação cliente**, clique em **Transferir**. Esta ação transfere um projeto Android concluído para uma aplicação pré-configurada para ligar ao back-end. 
2. Abra o projeto com o **Android Studio**, utilizando **Importar projeto (Eclipse ADT, Gradle, etc.)** . Verifique se seleciona esta importação para evitar eventuais erros do JDK.
3. Prima o botão **Executar “aplicação”** para criar o projeto e iniciar a aplicação no simulador do Android.
4. Na aplicação, digite um texto significativo, como *Concluir o tutorial* e, em seguida, clique no botão “Adicionar”. Esta ação envia um pedido POST ao back-end do Azure que implementou anteriormente. Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem SQL e devolve informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista. 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Portal do Azure]: https://portal.azure.com/
