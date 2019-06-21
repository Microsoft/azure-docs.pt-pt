---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184756"
---
1. No Explorador de soluções do Visual Studio, clique com botão direito do projeto de aplicação da Windows Store. Em seguida, selecione **Store** > **associar aplicação a Store**.

    ![Associar aplicação a Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. No assistente, selecione **seguinte**. Em seguida, inicie sessão com a sua conta Microsoft. Na **reservar um novo nome de aplicação**, escreva um nome para a sua aplicação e, em seguida, selecione **reserva**.
3. Quando o registo da aplicação é criado com êxito, selecione o novo nome de aplicação. Selecione **próxima**e, em seguida, selecione **associar**. Este processo adiciona as informações de registo do Windows Store necessárias para o manifesto do aplicativo.
4. Repita os passos 1 e 3 para o projeto de aplicação do Windows Phone Store utilizando o mesmo registo que criou anteriormente para a aplicação da Windows Store.  
5. Vá para o [Windows Dev Center](https://dev.windows.com/en-us/overview)e, em seguida, inicie sessão com a sua conta Microsoft. Na **as minhas aplicações**, selecione o novo registo de aplicações. Em seguida, expanda **serviços** > **notificações Push**.
6. Sobre o **notificações Push** página, em **Windows Push Notification Services (WNS) e o Microsoft Azure Mobile Apps**, selecione **site dos Serviços Live**.  Tome nota dos valores do **SID do pacote** e o *atual* valor no **segredo de aplicação**. 

    ![Definição de aplicação no Centro de programadores](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo da aplicação e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com qualquer pessoa ou distribuí-los com a sua aplicação.
   >
   >
