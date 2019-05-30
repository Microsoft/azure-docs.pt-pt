---
title: Considerações sobre o Xamarin iOS (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba mais sobre as considerações específicas ao utilizar o Xamarin iOS com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf236bff2300129ec97d3b8946c4c2a2748bca77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602141"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Considerações sobre o Xamarin iOS específicos com MSAL.NET
No Xamarin iOS, existem várias considerações que deve levar em conta quando com MSAL.NET

- [Problemas conhecidos com o iOS 12 e autenticação](#known-issues-with-ios-12-and-authentication)
- [Substituir e implemente o `OpenUrl` funcionar a `AppDelegate`](#implement-openurl)
- [Ativar grupos de Keychain](#enable-keychain-groups)
- [Ativar a partilha de cache de tokens](#enable-token-cache-sharing-across-ios-applications)
- [Ativar o acesso de Keychain](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemas conhecidos com o iOS 12 e autenticação
A Microsoft lançou uma [consultoria de segurança](https://github.com/aspnet/AspNetCore/issues/4647) para fornecer informações sobre uma incompatibilidade entre iOS12 e alguns tipos de autenticação. A incompatibilidade quebras social, WSFed e OIDC inícios de sessão. Esta recomendação também fornece orientações sobre o que os desenvolvedores podem fazer para remover restrições de segurança atual adicionadas pelo ASP.NET para seus aplicativos para se tornar compatível com iOS12.  

Ao desenvolver aplicativos de MSAL.NET no Xamarin iOS, poderá ver um loop infinito quando tentar iniciar sessão em Web sites do iOS 12 (semelhante a isto [problema da ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Também poderá ver uma quebra de autenticação do ASP.NET Core OIDC com o iOS 12 Safari, conforme descrito neste [WebKit problema](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementar OpenUrl

Primeiro precisa substituir os `OpenUrl` método da `FormsApplicationDelegate` derivado da classe e chamar `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Também tem de definir um esquema de URL, necessitam de permissões para a sua aplicação chamar outra aplicação, tem um formulário específico para o URL de redirecionamento e registar este URL de redirecionamento na [portal do Azure](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>Ativar grupos de KeyChain

Para fazer com que o token em cache o trabalho e ter o `AcquireTokenSilentAsync` trabalho de método, tem de ser seguidos vários passos:
1. Ativar o acesso de keychain no seu *`* Entitlements.plist* de ficheiro e especifique o **grupos de Keychain** no identificador do pacote.
2. Selecione *`*Entitlements.plist*`* de ficheiros a **elegibilidades personalizadas** campo no iOS projeto opções da janela **a vista de assinatura de pacote**.
3. Quando um certificado de assinatura, certifique-se de que xcode utiliza o mesmo ID Apple.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Ativar a cache de tokens partilha entre aplicações iOS

A partir de MSAL 2.x, pode especificar um grupo de segurança de keychain a utilizar para fazer a persistência da cache de token em várias aplicações. Isto permite-lhe partilhar a cache de token entre vários aplicativos, ter o mesmo grupo de segurança de keychain, inclusive os desenvolvidos com [ADAL.NET](https://aka.ms/adal-net), aplicações xamarin. IOS de MSAL.NET e aplicativos do iOS nativo com o [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

A cache de token de partilha permite o início de sessão único (SSO) entre todos os aplicativos que utilizam o mesmo grupo de segurança de keychain.

Para ativar o início de sessão único, tem de definir o `PublicClientApplication.iOSKeychainSecurityGroup` propriedade para o mesmo valor em todos os aplicativos.

Um exemplo desta situação através de MSAL v3.x seria:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Um exemplo desta situação através de MSAL v2.7.x seria:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> O `KeychainSecurityGroup` propriedade foi preterida. Anteriormente, no MSAL 2.x, os desenvolvedores eram forçados a incluir o prefixo de TeamId ao utilizar o `KeychainSecurityGroup` propriedade. 
> 
> Agora, a partir de MSAL 2.7.x, MSAL resolverá o prefixo de TeamId durante o tempo de execução ao utilizar o `iOSKeychainSecurityGroup` propriedade. Quando utilizar esta propriedade, o valor não deve conter o prefixo de TeamId. 
> 
> Utilizar a nova `iOSKeychainSecurityGroup` propriedade, que não requer que os desenvolvedores forneçam o TeamId. O `KeychainSecurityGroup` propriedade está obsoleta. 

## <a name="enable-keychain-access"></a>Ativar o acesso de keychain

No MSAL 2.x e 4.x da ADAL, a TeamId é utilizada para aceder a keychain, que permite que as bibliotecas de autenticação fornecer início de sessão único (SSO) entre as aplicações do mesmo publicador. 

O que é o [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId)? É um identificador exclusivo (da empresa ou pessoais) na App Store. O AppId é exclusivo para uma aplicação. Se tiver mais do que uma aplicação, o TeamId para todas as aplicações serão os mesmos, mas o AppId será diferente. O grupo de acesso de keychain tem como prefixo TeamId automaticamente para cada grupo pelo sistema. É como o sistema operacional impõe que as aplicações do mesmo publicador podem aceder a keychain partilhada. 

A inicializar o `PublicClientApplication`, se receber um `MsalClientException` com a mensagem: `TeamId returned null from the iOS keychain...`, terá de fazer o seguinte na aplicação Xamarin iOS:

1. No VS, no separador de depuração, vá para nameOfMyApp.iOS propriedades...
2. Em seguida, avance para o iOS assinatura do pacote 
3. Em elegibilidades personalizadas, clique na... e selecione o ficheiro de Entitlements.plist da sua aplicação
4. O ficheiro csproj da aplicação iOS, deverá ter esta linha agora incluída: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Reconstruir** o projeto.

Isso é *além disso* para ativar o acesso de keychain no `Entitlements.plist` de ficheiros, através de um a abaixo do grupo de acesso ou seu próprio:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>Passos Seguintes

Obter mais detalhes são fornecidos na [considerações específicas do iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) parágrafo de ficheiro de readme.md o exemplo seguinte:

Exemplo | Plataforma | Descrição 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Uma aplicação Xamarin Forms simples que mostra como utilizar a MSAL para autenticar MSA e o Azure AD com o ponto final V2.0 do AAD e acessar o Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-xamarin-ios-considerations/topology.png)