---
title: Considerações de Xamarin iOS (MSAL.NET) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para a utilização do Xamarin iOS com a Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 73303d64d7ebeb94290819226e5e5944f95f6e42
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165707"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considerações para a utilização de Xamarin iOS com MSAL.NET
Quando utilizar a Microsoft Authentication Library para .NET (MSAL.NET) no Xamarin iOS, deve: 

- Sobrepor e implementar a `OpenUrl` função em `AppDelegate` .
- Ativar grupos de chavechain.
- Ativar a partilha de cache token.
- Ativar o acesso ao chaveiro.
- Compreenda os problemas conhecidos com o iOS 12 e a autenticação.

## <a name="implement-openurl"></a>Implementar OpenUrl

Anular o `OpenUrl` método da classe `FormsApplicationDelegate` derivada e chamar `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Eis um exemplo:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Faça também as seguintes tarefas: 
* Defina um esquema de URL.
* Requer permissões para que a sua app ligue para outra aplicação.
* Tenha um formulário específico para o URL de redirecionamento.
* Registe o URL de redirecionamento no [portal Azure](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Permitir o acesso ao chaveiro

Para permitir o acesso ao chaveiro, certifique-se de que a sua aplicação tem um grupo de acesso chaveiro. Pode definir o grupo de acesso ao chaveiro quando criar a sua aplicação utilizando a `WithIosKeychainSecurityGroup()` API.

Para beneficiar da cache e do único sign-on (SSO), defina o grupo de acesso do chaveiro para o mesmo valor em todas as suas aplicações.

Este exemplo da configuração utiliza MSAL 4.x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Também ative o acesso do chaveiro no `Entitlements.plist` ficheiro. Utilize o seguinte grupo de acesso ou o seu próprio grupo de acesso.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Quando utiliza a `WithIosKeychainSecurityGroup()` API, a MSAL anexa automaticamente o seu grupo de segurança ao fim do *ID* da equipa da aplicação ( `AppIdentifierPrefix` ). A MSAL adiciona o seu grupo de segurança porque quando constrói a sua aplicação no Xcode, fará o mesmo. É por isso que os direitos no `Entitlements.plist` ficheiro precisam de ser incluídos antes do grupo de acesso ao `$(AppIdentifierPrefix)` chaveiro.

Para mais informações, consulte a [documentação sobre direitos iOS.](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps) 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Permitir a partilha de cache de token em aplicações iOS

A partir de MSAL 2.x, pode especificar um grupo de acesso de chaveiro para persistir a cache simbólica em várias aplicações. Esta definição permite-lhe partilhar a cache simbólica entre várias aplicações que têm o mesmo grupo de acesso ao chaveiro. Pode partilhar o dinheiro simbólico entre [ADAL.NET](https://aka.ms/adal-net) aplicações, aplicações MSAL.NET Xamarin.iOS e aplicações nativas do iOS que foram desenvolvidas em [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Ao partilhar a cache simbólica, permite um único sign-on (SSO) entre todas as aplicações que usam o mesmo grupo de acesso ao chaveiro.

Para permitir esta partilha de cache, utilize o `WithIosKeychainSecurityGroup()` método para definir o grupo de acesso do chaveiro ao mesmo valor em todas as aplicações que partilham a mesma cache. O primeiro exemplo de código neste artigo mostra como utilizar o método.

No início deste artigo, aprendeu que a MSAL adiciona `$(AppIdentifierPrefix)` sempre que utiliza a `WithIosKeychainSecurityGroup()` API. A MSAL adiciona este elemento porque o ID da equipa `AppIdentifierPrefix` garante que apenas as aplicações que são feitas pela mesma editora podem partilhar o acesso ao teclain.

> [!NOTE]
> A `KeychainSecurityGroup` propriedade está prevadida.
> 
> A partir de MSAL 2.x, os desenvolvedores foram forçados a incluir o `TeamId` prefixo quando usaram a `KeychainSecurityGroup` propriedade. Mas a partir de MSAL 2.7.x, quando você usa o novo `iOSKeychainSecurityGroup` imóvel, MSAL resolve o `TeamId` prefixo durante o tempo de funcionamento. Quando utilizar esta propriedade, não inclua o `TeamId` prefixo no valor. O prefixo não é necessário.
>
> Como a `KeychainSecurityGroup` propriedade é obsoleta, use a `iOSKeychainSecurityGroup` propriedade.

### <a name="use-microsoft-authenticator"></a>Utilize o Autenticador Microsoft

A sua aplicação pode utilizar o Microsoft Authenticator como corretor para ativar:

- **SSO**: Quando ativa o SSO, os seus utilizadores não precisam de iniciar sinas em cada aplicação.
- **Identificação do dispositivo**: Utilize a identificação do dispositivo para autenticar acedendo ao certificado do dispositivo. Este certificado é criado no dispositivo quando é ligado ao local de trabalho. A sua aplicação estará pronta se os administradores do arrendatário permitirem o acesso condicional relacionado com os dispositivos.
- **Verificação de identificação de aplicação**: Quando uma aplicação chama o corretor, passa o seu URL de redirecionamento. O corretor verifica o URL de redirecionamento.

Para obter mais informações sobre como ativar um corretor, consulte [use Microsoft Authenticator ou Microsoft Intune Company Portal em aplicações Xamarin iOS e Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemas conhecidos com iOS 12 e autenticação
A Microsoft lançou um [aviso de segurança](https://github.com/aspnet/AspNetCore/issues/4647) sobre uma incompatibilidade entre o iOS 12 e alguns tipos de autenticação. A incompatibilidade quebra os sign-ins sociais, WSFed e OIDC. O aviso de segurança ajuda os desenvolvedores a entender como remover ASP.NET restrições de segurança das suas aplicações para torná-las compatíveis com o iOS 12.  

Quando desenvolves aplicações MSAL.NET no Xamarin iOS, poderás ver um loop infinito quando tentas iniciar sôs-te nos websites do iOS 12. Este comportamento é semelhante a esta [questão ADAL.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329) 

Você também pode ver uma quebra na autenticação core OIDC ASP.NET com o iOS 12 Safari. Para mais informações, consulte esta [edição do WebKit.](https://bugs.webkit.org/show_bug.cgi?id=188165)

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre propriedades para xamarin iOS, consulte o parágrafo de [considerações específicas](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) para o iOS do ficheiro README.md da amostra seguinte:

Sample | Plataforma | Descrição
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Universal Windows Platform (UWP) | Uma aplicação simples Xamarin Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e AD AD AZure através do ponto final Azure AD 2.0. A aplicação também mostra como usar o token resultante para aceder ao Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->