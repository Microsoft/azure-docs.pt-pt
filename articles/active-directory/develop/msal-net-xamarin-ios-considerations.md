---
title: Considerações xamarin iOS (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao utilizar o Xamarin iOS com a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.custom: aaddev
ms.openlocfilehash: 7706a7f06cedde783e53a24ff385fa2376edcb93
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083549"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Considerações específicas do xamarin iOS com MSAL.NET
No xamarin iOS, existem várias considerações que deve ter em conta ao utilizar MSAL.NET

- [Questões conhecidas com o iOS 12 e autenticação](#known-issues-with-ios-12-and-authentication)
- [Sobrepor e implementar a função `OpenUrl` no `AppDelegate`](#implement-openurl)
- [Ativar grupos keychain](#enable-keychain-access)
- [Permitir a partilha de cache token](#enable-token-cache-sharing-across-ios-applications)
- [Ativar o acesso ao Keychain](#enable-keychain-access)

## <a name="implement-openurl"></a>Implementar OpenUrl

Primeiro, é necessário anular o método `OpenUrl` da classe derivada `FormsApplicationDelegate` e chamar `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Você também terá que definir um esquema de URL, exigir permissões para a sua app para ligar para outra app, ter um formulário específico para o URL de redirecionamento, e registar este URL de redirecionamento no [portal Azure](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Ativar o acesso ao porta-chaves

Para permitir o acesso ao porta-chaves, a sua aplicação deve ter um grupo de acesso à porta-chaves.
Pode definir o seu grupo de acesso ao keychain utilizando o `WithIosKeychainSecurityGroup()` api ao criar a sua aplicação como mostrado abaixo:

Para beneficiar da cache e da inscrição individual, é necessário definir o grupo de acesso ao porta-chaves com o mesmo valor em todas as suas aplicações.

Um exemplo disso usando MSAL v4.x seria:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Esta alteração é *além de* permitir o acesso à cadeia de chaves no ficheiro `Entitlements.plist`, utilizando o grupo de acesso abaixo ou o seu próprio:

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Quando utiliza o `WithIosKeychainSecurityGroup()` api, a MSAL afixa automaticamente o seu grupo de segurança ao fim do ID da *equipa* da aplicação (AppIdentifierPrefix) porque quando constrói a sua aplicação utilizando o xcode, fará o mesmo. Para mais informações, consulte a documentação dos [direitos do iOS.](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps) É por isso que os direitos devem incluir `$(AppIdentifierPrefix)` antes do grupo de acesso à cadeia de chaves no `Entitlements.plist`.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Permitir a partilha de cache simbólica através de aplicações iOS

A partir de MSAL 2.x, pode especificar um Grupo de Acesso à Cadeia de Chaves para utilizar para persistir a cache simbólica em várias aplicações. Esta definição permite-lhe partilhar a cache simbólica entre várias aplicações com o mesmo grupo de acesso à porta-chaves, incluindo as desenvolvidas com [ADAL.NET](https://aka.ms/adal-net), MSAL.NET aplicações Xamarin.iOS e aplicações nativas do iOS desenvolvidas com [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc).](https://github.com/AzureAD/microsoft-authentication-library-for-objc)

A partilha da cache simbólica permite um único sinal entre todas as aplicações que utilizam o mesmo Grupo de acesso à Keychain.

Para permitir esta partilha de cache, é necessário definir o método 'WithIosKeychainSecurityGroup()' para definir o grupo de acesso à porta-chaves para o mesmo valor em todas as aplicações que partilhem a mesma cache que se pode mostrar no exemplo acima.

Anteriormente, foi mencionado que a MSAL adicionava o $(AppIdentifierPrefix) sempre que utilizasse o `WithIosKeychainSecurityGroup()` api. Isto porque o AppIdentifierPrefix ou o "ID da equipa" são utilizados para garantir que apenas as aplicações feitas pela mesma editora possam partilhar o acesso ao keychain.

> [!NOTE]
> **A propriedade `KeychainSecurityGroup` é depreciada.**
> 
> Anteriormente, a partir de MSAL 2.x, os desenvolvedores foram forçados a incluir o prefixo TeamId ao usar a propriedade `KeychainSecurityGroup`.
>
>  A partir de MSAL 2.7.x, ao utilizar a nova propriedade `iOSKeychainSecurityGroup`, a MSAL resolverá o prefixo TeamId durante o tempo de execução. Ao utilizar esta propriedade, o valor não deve conter o prefixo TeamId.
>  Utilize a nova propriedade `iOSKeychainSecurityGroup`, que não requer que forneça o TeamId, uma vez que a propriedade anterior `KeychainSecurityGroup` está agora obsoleta.

### <a name="use-microsoft-authenticator"></a>Use o autenticador microsoft

A sua aplicação pode utilizar o Microsoft Authenticator (um corretor) para ativar:

- Sinal único (SSO). Os seus utilizadores não precisarão de iniciar sessão em cada aplicação.
- Identificação do dispositivo. Acedendo ao certificado do dispositivo, que foi criado no dispositivo quando era local de trabalho. A sua aplicação estará pronta se os administradores do arrendatário permitirem o Acesso Condicional relacionado com os dispositivos.
- Verificação de identificação de aplicação. Quando uma aplicação chama o corretor, passa a sua url de redirecionamento, e o corretor verifica-o.

Para mais detalhes sobre como ativar o corretor, consulte [Use Microsoft Authenticator ou Microsoft Intune portal da empresa nas aplicações Xamarin iOS e Android](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Amostra ilustrando propriedades específicas do Xamarin iOS

Mais detalhes são fornecidos no parágrafo [iOS Considerações Específicas](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) do ficheiro readme.md da amostra seguinte:

Sample | Plataforma | Descrição
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Uma simples aplicação Xamarin Forms que mostra como usar o MSAL para autenticar a MSA e a AD Azure através do ponto final do Azure AD V2.0, e aceder ao Microsoft Graph com o símbolo resultante.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->

## <a name="known-issues-with-ios-12-and-authentication"></a>Questões conhecidas com o iOS 12 e autenticação
A Microsoft lançou um aviso de [segurança](https://github.com/aspnet/AspNetCore/issues/4647) para fornecer informações sobre uma incompatibilidade entre o iOS12 e alguns tipos de autenticação. A incompatibilidade quebra os logins sociais, WSFed e OIDC. Este aviso também fornece orientações sobre o que os desenvolvedores podem fazer para remover as atuais restrições de segurança adicionadas por ASP.NET às suas aplicações para se tornarem compatíveis com o iOS12.  

Ao desenvolver aplicações MSAL.NET no Xamarin iOS, poderá ver um loop infinito ao tentar iniciar sessão em websites do iOS 12 (semelhante a este [problema ADAL).](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329) 

Também poderá ver uma rutura na autenticação core OIDC ASP.NET com o iOS 12 Safari, conforme descrito nesta [edição do WebKit.](https://bugs.webkit.org/show_bug.cgi?id=188165)
