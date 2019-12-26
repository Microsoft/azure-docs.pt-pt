---
title: Autenticação e autorização
description: Referência conceitual e visão geral do recurso de autenticação/autorização para Azure App serviço, especificamente para aplicativos móveis.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 2c3bc1f9563a5657d7574020fc908f23a711391c
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668521"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autenticação e autorização no serviço de Azure App para aplicativos móveis

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

Este artigo descreve como funciona a autenticação e a autorização ao desenvolver aplicativos móveis nativos com um back-end do serviço de aplicativo. O serviço de aplicativo fornece autenticação e autorização integradas, para que seus aplicativos móveis possam conectar usuários sem alterar nenhum código no serviço de aplicativo. Ele fornece uma maneira fácil de proteger seu aplicativo e trabalhar com dados por usuário. 

Este artigo se concentra no desenvolvimento de aplicativos móveis. Para começar rapidamente com a autenticação e autorização do serviço de aplicativo para seu aplicativo móvel, consulte um dos tutoriais a seguir [Adicionar autenticação ao aplicativo IOS][iOS] (ou [Xamarin.Android], [Windows], [Xamarin.iOS], Xamarin. [Android], [Xamarin.Forms]ou [Cordova]). 

Para obter informações sobre como a autenticação e a autorização funcionam no serviço de aplicativo, consulte [autenticação e autorização no serviço de Azure app](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Autenticação com o SDK do provedor

Depois que tudo estiver configurado no serviço de aplicativo, você poderá modificar os clientes móveis para entrar com o serviço de aplicativo. Há duas abordagens aqui:

* Use um SDK que um determinado provedor de identidade publica para estabelecer a identidade e, em seguida, obter acesso ao serviço de aplicativo.
* Use uma única linha de código para que o SDK do cliente dos aplicativos móveis possa conectar usuários.

> [!TIP]
> A maioria dos aplicativos deve usar um SDK de provedor para obter uma experiência mais consistente quando os usuários entram, para usar o suporte à atualização de token e para obter outros benefícios que o provedor especifica.
> 
> 

Quando você usa um SDK do provedor, os usuários podem entrar em uma experiência que se integre mais rigidamente ao sistema operacional em que o aplicativo está sendo executado. Esse método também fornece um token de provedor e algumas informações de usuário sobre o cliente, o que torna muito mais fácil consumir as APIs do grafo e personalizar a experiência do usuário. Ocasionalmente, em Blogs e fóruns, ele é chamado de "fluxo do cliente" ou "fluxo direcionado pelo cliente", pois o código no cliente entra em usuários e o código do cliente tem acesso a um token do provedor.

Depois que um token de provedor é obtido, ele precisa ser enviado ao serviço de aplicativo para validação. Depois que o serviço de aplicativo valida o token, o serviço de aplicativo cria um novo token do serviço de aplicativo que é retornado ao cliente. O SDK do cliente dos aplicativos móveis tem métodos auxiliares para gerenciar essa troca e anexar automaticamente o token a todas as solicitações para o back-end do aplicativo. Os desenvolvedores também podem manter uma referência ao token do provedor.

Para obter mais informações sobre o fluxo de autenticação, consulte [fluxo de autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autenticação sem o SDK do provedor

Se você não quiser configurar um SDK do provedor, poderá permitir que o recurso aplicativos móveis do serviço Azure App para entrar para você. O SDK do cliente dos aplicativos móveis abrirá uma exibição da Web para o provedor de sua escolha e entrará no usuário. Ocasionalmente, em Blogs e fóruns, ele é chamado de "fluxo do servidor" ou "fluxo direcionado ao servidor", pois o servidor gerencia o processo que conecta os usuários e o SDK do cliente nunca recebe o token do provedor.

O código para iniciar esse fluxo está incluído no tutorial de autenticação para cada plataforma. No final do fluxo, o SDK do cliente tem um token do serviço de aplicativo e o token é anexado automaticamente a todas as solicitações para o back-end do aplicativo.

Para obter mais informações sobre o fluxo de autenticação, consulte [fluxo de autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Mais recursos

Os tutoriais a seguir mostram como adicionar autenticação aos seus clientes móveis usando o [fluxo direcionado ao servidor](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Adicionar autenticação ao seu aplicativo iOS][iOS]
* [Adicionar autenticação ao seu aplicativo Android][Android]
* [Adicionar autenticação ao seu aplicativo do Windows][Windows]
* [Adicionar autenticação ao aplicativo Xamarin. iOS][Xamarin.iOS]
* [Adicionar autenticação ao seu aplicativo Xamarin. Android][Xamarin.Android]
* [Adicionar autenticação ao aplicativo Xamarin. Forms][Xamarin.Forms]
* [Adicionar autenticação ao seu aplicativo Cordova][Cordova]

Use os seguintes recursos se desejar usar o [fluxo direcionado ao cliente](../app-service/overview-authentication-authorization.md#authentication-flow) para Azure Active Directory:

* [Usar o Biblioteca de Autenticação do Active Directory para iOS][ADAL-iOS]
* [Usar o Biblioteca de Autenticação do Active Directory para Android][ADAL-Android]
* [Usar o Biblioteca de Autenticação do Active Directory para Windows e Xamarin][ADAL-dotnet]

Use os seguintes recursos se você quiser usar o [fluxo direcionado ao cliente](../app-service/overview-authentication-authorization.md#authentication-flow) para o Facebook:

* [Usar o SDK do Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Use os seguintes recursos se você quiser usar o [fluxo direcionado ao cliente](../app-service/overview-authentication-authorization.md#authentication-flow) para o Twitter:

* [Usar a malha do Twitter para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Use os seguintes recursos se você quiser usar o [fluxo direcionado ao cliente](../app-service/overview-authentication-authorization.md#authentication-flow) para o Google:

* [Usar o SDK de entrada do Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
