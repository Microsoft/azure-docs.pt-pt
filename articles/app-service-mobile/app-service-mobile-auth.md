---
title: Autenticação e autorização no serviço de aplicações do Azure para aplicações móveis | Documentos da Microsoft
description: Referência conceptual e descrição geral da autenticação / autorização de recursos para o serviço de aplicações do Azure, especificamente para aplicações móveis
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859129"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autenticação e autorização no serviço de aplicações do Azure para aplicações móveis

Este artigo descreve como a autenticação e autorização funciona durante o desenvolvimento de aplicações móveis nativas com um back-end de serviço de aplicações. Serviço de aplicações fornece integrada de autenticação e autorização, para que as suas aplicações móveis podem iniciar utilizadores sem alterar nenhum código no serviço de aplicações. Ele fornece uma forma fácil de proteger a sua aplicação e trabalhar com dados de por utilizador. 

Este artigo se concentra no desenvolvimento de aplicações móveis. Para começar rapidamente com a autenticação de serviço de aplicações e autorização para a sua aplicação móvel, consulte um dos seguintes tutoriais [adicionar autenticação à sua aplicação iOS] [ iOS] (ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], ou [Cordova]). 

Para obter informações sobre como funcionam os autenticação e autorização no serviço de aplicações, consulte [autenticação e autorização no serviço de aplicações do Azure](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Autenticação com o fornecedor de SDK

Depois de tudo o que é configurado no serviço de aplicações, pode modificar os clientes móveis para iniciar sessão com o serviço de aplicações. Existem duas abordagens aqui:

* Utilize um SDK que um fornecedor de identidade determinado publica para estabelecer a identidade e, em seguida, obter acesso ao serviço de aplicações.
* Utilize uma única linha de código para que o SDK do cliente de Mobile Apps pode iniciar sessão dos utilizadores.

> [!TIP]
> A maioria dos aplicativos devem usar um fornecedor de SDK para obter uma experiência mais consistente quando os utilizadores iniciam sessão, utilizar o suporte de atualização de token e obter outros benefícios que especifica o fornecedor.
> 
> 

Quando utiliza um fornecedor de SDK, os utilizadores podem iniciar sessão para uma experiência que integra-se mais rigidamente com o sistema operativo que a aplicação está em execução no. Esse método também lhe dá um token de fornecedor e algumas informações de utilizador do cliente, o que torna muito mais fácil de consumir graph APIs e personalizar a experiência do usuário. Ocasionalmente em blogs e fóruns, ele é referido como o "fluxo de cliente" ou "cliente direcionado fluxo" porque o código nos sinais de cliente dos utilizadores e o código de cliente tem acesso a um token de fornecedor.

Depois de um token de fornecedor é obtido, ele precisa ser enviada ao serviço de aplicações para a validação. Depois do serviço de aplicações valida o token, o serviço de aplicações cria um novo token de serviço de aplicações, que é devolvido ao cliente. O SDK do cliente de Mobile Apps tem métodos auxiliares para gerir este exchange e anexar automaticamente o token para todos os pedidos para o back-end do aplicativo. Os desenvolvedores também podem manter uma referência para o token de fornecedor.

Para obter mais informações sobre o fluxo de autenticação, consulte [fluxo de autenticação do serviço de aplicações](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autenticação sem o fornecedor de SDK

Se não pretender configurar um fornecedor de SDK, pode permitir a funcionalidade de aplicações móveis do serviço de aplicações do Azure para iniciar sessão para. O SDK do cliente de Mobile Apps irá abrir uma vista web para o fornecedor de sua escolha e iniciar a sessão do utilizador. Ocasionalmente em blogs e fóruns, ele é chamado o "fluxo de servidor" ou "direcionado de servidor de fluxo" porque o servidor gere o processo que inicia sessão dos utilizadores e o SDK do cliente nunca recebe o token de fornecedor.

Código para iniciar este fluxo está incluído no tutorial de autenticação para cada plataforma. No final do fluxo, o SDK do cliente tem um token do serviço de aplicações e o token é conectado automaticamente a todos os pedidos para o back-end de aplicação.

Para obter mais informações sobre o fluxo de autenticação, consulte [fluxo de autenticação do serviço de aplicações](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Mais recursos

Os tutoriais seguintes mostram como adicionar autenticação para os seus clientes móveis ao utilizar o [direcionado de servidor de fluxo](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Adicionar autenticação à sua aplicação iOS][iOS]
* [Adicionar autenticação à sua aplicação Android][Android]
* [Adicionar autenticação à sua aplicação do Windows][Windows]
* [Adicionar autenticação à sua aplicação xamarin. IOS][xamarin.ios]
* [Adicionar autenticação à sua aplicação xamarin. Android][xamarin.android]
* [Adicionar autenticação à sua aplicação xamarin. Forms][xamarin.forms]
* [Adicionar autenticação à sua aplicação Cordova][Cordova]

Utilize os seguintes recursos se pretender utilizar o [fluxo de cliente direcionado](../app-service/overview-authentication-authorization.md#authentication-flow) para o Azure Active Directory:

* [Utilizar o Active Directory Authentication Library para iOS][ADAL-iOS]
* [Utilizar a biblioteca de autenticação do Active Directory para Android][ADAL-Android]
* [Utilizar a biblioteca de autenticação do Active Directory do Windows e Xamarin][ADAL-dotnet]

Utilize os seguintes recursos se pretender utilizar o [fluxo de cliente direcionado](../app-service/overview-authentication-authorization.md#authentication-flow) para o Facebook:

* [Utilizar o SDK do Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Utilize os seguintes recursos se pretender utilizar o [fluxo de cliente direcionado](../app-service/overview-authentication-authorization.md#authentication-flow) do Twitter:

* [Utilizar recursos de infraestrutura do Twitter para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Utilize os seguintes recursos se pretender utilizar o [fluxo de cliente direcionado](../app-service/overview-authentication-authorization.md#authentication-flow) para o Google:

* [Utilizar o SDK do início de sessão no Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
