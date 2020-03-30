---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184759"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Configure o projeto iOS no Estúdio Xamarin
1. Em Xamarin.Studio, abra **info.plist**e atualize o **Bundle Identifier** com o ID do pacote que criou anteriormente com o seu novo ID de aplicação.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Percorra para baixo para **modos**de fundo . Selecione a caixa **de modos de fundo ativar** e a caixa de **notificações remotas.**

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Clique duas vezes no seu projeto no Painel de Soluções para abrir opções de **projeto.**
4. Em **Build**, escolha **iOS Bundle Signing,** e selecione o perfil de identidade e provisionamento correspondente que acabou de configurar para este projeto.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Isto garante que o projeto utiliza o novo perfil para a assinatura de código. Para obter a documentação oficial de fornecimento de dispositivos Xamarin, consulte o [fornecimento de dispositivos Xamarin].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Configure o projeto iOS no Estúdio Visual
1. No Estúdio Visual, clique no projeto e clique em **Propriedades**.
2. Nas páginas de propriedades, clique no separador de **aplicação iOS** e atualize o **Identificador** com o ID que criou anteriormente.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. No **separador iOS Bundle Signing,** selecione o perfil de identidade e provisionamento correspondente que acabou de configurar para este projeto.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Isto garante que o projeto utiliza o novo perfil para a assinatura de código. Para obter a documentação oficial de fornecimento de dispositivos Xamarin, consulte o [fornecimento de dispositivos Xamarin].
4. Clique duas vezes na info.plist para abri-la e, em seguida, ativar **Notificações Remotas** em **modos**de fundo .

[Fornecimento de dispositivos Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
