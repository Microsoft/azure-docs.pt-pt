---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140092"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Configurar o projeto do iOS no Xamarin Studio
1. No Xamarin.Studio, abra **Info. plist**e atualizar a **identificador de pacote** com o ID do pacote que criou anteriormente com o novo ID de aplicação.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Desloque para baixo até **modos em segundo plano**. Selecione o **ativar modos de segundo plano** caixa e o **notificações remotas** caixa.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Faça duplo clique em seu projeto no painel de solução para abrir **opções do projeto**.
4. Sob **crie**, escolha **iOS assinatura do pacote**e selecionar a identidade correspondente e o perfil de aprovisionamento conjunto de apenas cópia de segurança para este projeto.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Isto garante que o projeto utiliza o novo perfil de assinatura de código. Para o dispositivo Xamarin oficial, documentação de aprovisionamento, consulte [aprovisionamento de dispositivos do Xamarin].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Configurar o projeto do iOS no Visual Studio
1. No Visual Studio, com o botão direito no projeto e, em seguida, clique em **propriedades**.
2. Nas páginas de propriedades, clique nas **aplicação iOS** separador e atualizar a **identificador** com o ID que criou anteriormente.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Na **pacote de assinatura de iOS** separador, selecione a identidade correspondente e acabou de configurar para este projeto de perfil de aprovisionamento.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Isto garante que o projeto utiliza o novo perfil de assinatura de código. Para o dispositivo Xamarin oficial, documentação de aprovisionamento, consulte [aprovisionamento de dispositivos do Xamarin].
4. Faça duplo clique no ficheiro info. plist para abri-lo e, em seguida, ative **RemoteNotifications** sob **modos em segundo plano**.

[Aprovisionamento de dispositivos do Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
