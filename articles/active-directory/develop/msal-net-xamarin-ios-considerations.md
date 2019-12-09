---
title: Considerações sobre o Xamarin iOS (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao usar o Xamarin iOS com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f04074dfd9055fa4791f6fdce6bcf296aae8ff61
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921471"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Considerações específicas do Xamarin iOS com MSAL.NET
No Xamarin iOS, há várias considerações que você deve levar em conta ao usar o MSAL.NET

- [Problemas conhecidos com o iOS 12 e a autenticação](#known-issues-with-ios-12-and-authentication)
- [Substituir e implementar a função `OpenUrl` no `AppDelegate`](#implement-openurl)
- [Habilitar grupos de conjunto de chaves](#enable-keychain-access)
- [Habilitar compartilhamento de cache de token](#enable-token-cache-sharing-across-ios-applications)
- [Habilitar o acesso ao conjunto de chaves](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemas conhecidos com o iOS 12 e a autenticação
A Microsoft lançou um [comunicado de segurança](https://github.com/aspnet/AspNetCore/issues/4647) para fornecer informações sobre uma incompatibilidade entre iOS12 e alguns tipos de autenticação. A incompatibilidade interrompe os logons sociais, WSFed e OIDC. Este comunicado também fornece orientação sobre o que os desenvolvedores podem fazer para remover as restrições de segurança atuais adicionadas pelo ASP.NET a seus aplicativos para se tornarem compatíveis com o iOS12.  

Ao desenvolver aplicativos MSAL.NET no Xamarin iOS, você poderá ver um loop infinito ao tentar entrar em sites do iOS 12 (semelhante a esse problema de [Adal](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Você também pode ver uma interrupção no ASP.NET Core autenticação OIDC com o iOS 12 Safari, conforme descrito neste [problema de WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementar OpenUrl

Primeiro, você precisa substituir o método `OpenUrl` da classe derivada `FormsApplicationDelegate` e chamar `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Você também precisará definir um esquema de URL, exigir permissões para que seu aplicativo chame outro aplicativo, ter um formulário específico para a URL de redirecionamento e registrar essa URL de redirecionamento no [portal do Azure](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Habilitar o acesso ao conjunto de chaves

Para habilitar o acesso ao conjunto de chaves, seu aplicativo deve ter um grupo de acesso de cadeia de chaves.
Você pode definir o grupo de acesso do conjunto de chaves usando a API `WithIosKeychainSecurityGroup()` ao criar seu aplicativo, conforme mostrado abaixo:

Para habilitar o logon único, você precisa definir a propriedade `PublicClientApplication.iOSKeychainSecurityGroup` com o mesmo valor em todos os aplicativos.

Um exemplo disso é usar MSAL v3. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Os direitos. plist devem ser atualizados para se parecer com o fragmento XML a seguir:

Essa alteração é *além* de habilitar o acesso ao conjunto de chaves no arquivo de `Entitlements.plist`, usando o grupo de acesso abaixo ou seu próprio:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

Um exemplo disso é usar MSAL v4. x:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

Ao usar a API de `WithIosKeychainSecurityGroup()`, o MSAL acrescentará automaticamente o grupo de segurança ao final da "ID da equipe" (AppIdentifierPrefix) do aplicativo porque, quando você criar seu aplicativo usando o Xcode, ele fará o mesmo. [Consulte a documentação de direitos do IOS para obter mais detalhes](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). É por isso que você precisa atualizar os direitos para incluir $ (AppIdentifierPrefix) antes do grupo de acesso do conjunto de chaves no direito. plist.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Habilitar o compartilhamento de cache de token em aplicativos iOS

Do MSAL 2. x, você pode especificar um grupo de acesso de conjunto de chaves a ser usado para manter o cache do token entre vários aplicativos. Essa configuração permite que você compartilhe o cache de token entre vários aplicativos com o mesmo grupo de acesso do conjunto de chaves, incluindo aqueles desenvolvidos com aplicativos [Adal.net](https://aka.ms/adal-net), MSAL.net Xamarin. Ios e aplicativos Ios nativos desenvolvidos com [Adal. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL. ObjC](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

O compartilhamento do cache de token permite o logon único entre todos os aplicativos que usam o mesmo grupo de acesso do conjunto de chaves.

Para habilitar esse compartilhamento de cache, você precisa definir o uso do método ' WithIosKeychainSecurityGroup () ' para definir o grupo de acesso do conjunto de chaves com o mesmo valor em todos os aplicativos que compartilham o mesmo cache, conforme mostrado no exemplo acima.

Anteriormente, foi mencionado que o MSAL adicionou o $ (AppIdentifierPrefix) sempre que você usa a API `WithIosKeychainSecurityGroup()`. Isso ocorre porque o AppIdentifierPrefix ou a "ID da equipe" é usado para garantir que somente os aplicativos feitos pelo mesmo editor possam compartilhar o acesso ao conjunto de chaves.

> [!NOTE]
> **A propriedade `KeychainSecurityGroup` é preterida.**
> 
> Anteriormente, do MSAL 2. x, os desenvolvedores foram forçados a incluir o prefixo TeamID ao usar a propriedade `KeychainSecurityGroup`.
>
>  De MSAL 2.7. x, ao usar a nova propriedade `iOSKeychainSecurityGroup`, MSAL resolverá o prefixo TeamID durante o tempo de execução. Ao usar essa propriedade, o valor não deve conter o prefixo TeamID.
>  Use a nova propriedade `iOSKeychainSecurityGroup`, que não exige que você forneça o TeamID, pois a propriedade anterior `KeychainSecurityGroup` agora é obsoleta.

### <a name="use-microsoft-authenticator"></a>Usar Microsoft Authenticator

Seu aplicativo pode usar Microsoft Authenticator (um agente) para habilitar:

- SSO (logon único). Os usuários não precisarão entrar em cada aplicativo.
- Identificação do dispositivo. Acessando o certificado do dispositivo, que foi criado no dispositivo quando ele foi ingressado no local de trabalho. Seu aplicativo estará pronto se os administradores de locatário habilitarem o acesso condicional relacionado aos dispositivos.
- Verificação da identificação do aplicativo. Quando um aplicativo chama o agente, ele passa sua URL de redirecionamento e o agente o verifica.

Para obter detalhes sobre como habilitar o agente, consulte [usar o Microsoft Authenticator ou Microsoft Intune portal da empresa em aplicativos do Xamarin Ios e Android](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Exemplo ilustrando propriedades específicas do Xamarin iOS

Mais detalhes são fornecidos no parágrafo [Considerações específicas do IOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) do seguinte arquivo readme.MD do exemplo:

Exemplo | Plataforma | Descrição
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Um aplicativo Xamarin Forms simples mostrando como usar o MSAL para autenticar o MSA e o Azure AD por meio do ponto de extremidade V 2.0 do Azure AD e acessar o Microsoft Graph com o token resultante.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
