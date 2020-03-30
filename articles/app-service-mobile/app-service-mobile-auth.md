---
title: Autenticação e autorização
description: Referência conceptual e visão geral da funcionalidade de Autenticação/Autorização para o Serviço de Aplicações Azure, especificamente para aplicações móveis.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4a9ef62178b9a58fa8703413a09114a617d1d239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459468"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autenticação e autorização no Serviço de Aplicações do Azure para aplicações móveis

Este artigo descreve como a autenticação e a autorização funcionam no desenvolvimento de aplicações móveis nativas com um back back back Service. O Serviço de Aplicações fornece autenticação e autorização integradas, para que as suas aplicações móveis possam inscrever os utilizadores sem alterar qualquer código no Serviço de Aplicações. Fornece uma forma fácil de proteger a sua aplicação e trabalhar com dados por utilizador. 

Este artigo centra-se no desenvolvimento de aplicações móveis. Para começar rapidamente com a autenticação e autorização do App Service para a sua aplicação móvel, consulte um dos seguintes tutoriais [Adicione autenticação à sua aplicação iOS][iOS] (ou [Android,] [Windows,] [Xamarin.iOS,] [Xamarin.Android,] [Xamarin.Forms,]ou [Cordova).] 

Para obter informações sobre como a autenticação e autorização funcionam no Serviço de Aplicações, consulte autenticação e autorização no Serviço de [Aplicações Azure](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Autenticação com fornecedor SDK

Depois de tudo estar configurado no Serviço de Aplicações, pode modificar os clientes móveis para iniciar sessão com o Serviço de Aplicações. Há duas abordagens aqui:

* Utilize um SDK que um determinado fornecedor de identidade publique para estabelecer identidade e, em seguida, ter acesso ao Serviço de Aplicações.
* Utilize uma única linha de código para que o cliente de Aplicações Móveis SDK possa inscrever os utilizadores.

> [!TIP]
> A maioria das aplicações deve utilizar um SDK fornecedor para obter uma experiência mais consistente quando os utilizadores se inscrevem, para usar suporte de atualização de token, e para obter outros benefícios que o fornecedor especifica.
> 
> 

Quando utiliza um SDK fornecedor, os utilizadores podem iniciar sessão numa experiência que se integre mais fortemente com o sistema operativo em que a aplicação está a funcionar. Este método também lhe dá um token fornecedor e algumas informações do utilizador sobre o cliente, o que torna muito mais fácil consumir APIs de gráfico e personalizar a experiência do utilizador. Ocasionalmente em blogs e fóruns, é referido como o "fluxo de cliente" ou "fluxo direcionado para o cliente" porque o código nos sinais do cliente nos utilizadores, e o código do cliente tem acesso a um token do fornecedor.

Depois de obtido um token fornecedor, este tem de ser enviado para o Serviço de Aplicações para validação. Depois de o Serviço de Aplicações validar o token, o App Service cria um novo token do Serviço de Aplicações que é devolvido ao cliente. O cliente de Aplicações Móveis SDK tem métodos de ajuda para gerir esta troca e automaticamente anexar o símbolo a todos os pedidos ao back end da aplicação. Os desenvolvedores também podem manter uma referência ao token do fornecedor.

Para obter mais informações sobre o fluxo de autenticação, consulte o fluxo de [autenticação do Serviço de Aplicações](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autenticação sem fornecedor SDK

Se não quiser configurar um SDK fornecedor, pode permitir que a funcionalidade de Aplicações Móveis do Serviço de Aplicações Azure assine o seu contrato. O cliente de Aplicações Móveis SDK abrirá uma visão web ao fornecedor da sua escolha e assinará no utilizador. Ocasionalmente em blogs e fóruns, é chamado de "fluxo de servidor" ou "fluxo direcionado para servidores" porque o servidor gere o processo que assina nos utilizadores, e o cliente SDK nunca recebe o token do fornecedor.

O código para iniciar este fluxo está incluído no tutorial de autenticação para cada plataforma. No final do fluxo, o cliente SDK tem um token app service, e o token é automaticamente anexado a todos os pedidos ao backend da aplicação.

Para obter mais informações sobre o fluxo de autenticação, consulte o fluxo de [autenticação do Serviço de Aplicações](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Mais recursos

Os seguintes tutoriais mostram como adicionar autenticação aos seus clientes móveis utilizando o fluxo dirigido pelo [servidor:](../app-service/overview-authentication-authorization.md#authentication-flow)

* [Adicione a autenticação à sua aplicação iOS][iOS]
* [Adicione autenticação à sua aplicação Android][Android]
* [Adicione autenticação à sua aplicação Windows][Windows]
* [Adicione a autenticação à sua aplicação Xamarin.iOS][Xamarin.iOS]
* [Adicione a autenticação à sua aplicação Xamarin.Android][Xamarin.Android]
* [Adicione a autenticação à sua aplicação Xamarin.Forms][Xamarin.Forms]
* [Adicione autenticação à sua app Cordova][Cordova]

Utilize os seguintes recursos se pretender utilizar o [fluxo dirigido](../app-service/overview-authentication-authorization.md#authentication-flow) pelo cliente para o Diretório Ativo Azure:

* [Utilize a Biblioteca de Autenticação do Diretório Ativo para iOS][ADAL-iOS]
* [Utilize a Biblioteca de Autenticação de Diretório Ativo para Android][ADAL-Android]
* [Utilize a Biblioteca de Autenticação de Diretório Ativo para Windows e Xamarin][ADAL-dotnet]

Utilize os seguintes recursos se pretender utilizar o [fluxo dirigido](../app-service/overview-authentication-authorization.md#authentication-flow) pelo cliente para o Facebook:

* [Utilize o Facebook SDK para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Utilize os seguintes recursos se pretender utilizar o [fluxo dirigido](../app-service/overview-authentication-authorization.md#authentication-flow) pelo cliente para o Twitter:

* [Use tecido twitter para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Utilize os seguintes recursos se pretender utilizar o [fluxo dirigido](../app-service/overview-authentication-authorization.md#authentication-flow) pelo cliente para o Google:

* [Utilize o SDK de inscrição no Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
