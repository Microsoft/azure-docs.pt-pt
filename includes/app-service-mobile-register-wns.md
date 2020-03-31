---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184756"
---
1. No Visual Studio Solution Explorer, clique no projeto da aplicação Da Windows Store. Em seguida, selecione **Store** > **Associate App com a Loja**.

    ![Aplicativo associado com Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. No assistente, selecione **Seguinte**. Em seguida, inscreva-se na sua conta Microsoft. No Reserve um novo nome de **aplicação,** escreva um nome para a sua aplicação e, em seguida, selecione **Reserve**.
3. Depois de o registo da aplicação ser criado com sucesso, selecione o novo nome da aplicação. Selecione **Seguinte**, e, em seguida, selecione **Associate**. Este processo adiciona as informações de registo necessárias ao manifesto de inscrição da Windows Store no manifesto de aplicação.
4. Repita os passos 1 e 3 para o projeto da aplicação Windows Phone Store utilizando o mesmo registo que criou anteriormente para a aplicação Windows Store.  
5. Vá ao [Windows Dev Center](https://dev.windows.com/en-us/overview)e, em seguida, inscreva-se na sua conta Microsoft. Nas **minhas apps,** selecione o registo da nova aplicação. Em seguida, expandir**as notificações de Push**de **Serviços** > .
6. Na página de **notificações Push,** nos Serviços de **Notificação push do Windows (WNS) e nas aplicações móveis microsoft Azure,** selecione **site de Serviços Ao Vivo**.  Tome nota dos valores do **Pacote SID** e do valor *atual* em Segredo de **Aplicação**. 

    ![Definição de aplicativos no centro de desenvolvimento](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo da aplicação e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém nem os distribua com a sua aplicação.
   >
   >
