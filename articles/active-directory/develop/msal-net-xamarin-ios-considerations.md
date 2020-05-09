---
title: Considerações xamarin iOS (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para a utilização do xamarin iOS com a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.openlocfilehash: 7125559dd39e1626634dae7c45b0744bfff57d8c
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652660"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considerações para a utilização do iOS Xamarin com MSAL.NET
Quando utilizar a Microsoft Authentication Library para .NET (MSAL.NET) no Xamarin iOS, deve: 

- Sobrepor e `OpenUrl` implementar `AppDelegate`a função em .
- Ativar grupos de porta-chaves.
- Permitir a partilha de cache simbólica.
- Ativar o acesso ao porta-chaves.
- Compreenda as questões conhecidas com o iOS 12 e a autenticação.

## <a name="implement-openurl"></a>Implementar OpenUrl

Anular o `OpenUrl` método `FormsApplicationDelegate` da classe derivada e chamar `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`. Segue-se um exemplo:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Faça também as seguintes tarefas: 
* Defina um esquema de URL.
* Exija permissões para que a sua aplicação ligue para outra aplicação.
* Tenha um formulário específico para o URL redirecionado.
* Registe o URL de redirecionamento no [portal Azure](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Ativar o acesso ao porta-chaves

Para ativar o acesso ao keychain, certifique-se de que a sua aplicação tem um grupo de acesso à porta-chaves. Pode configurar o grupo de acesso ao porta-chaves quando criar a sua aplicação utilizando a `WithIosKeychainSecurityGroup()` API.

Para beneficiar da cache e da inscrição única (SSO), coloque o grupo de acesso à porta-chaves com o mesmo valor em todas as suas aplicações.

Este exemplo da configuração utiliza MSAL 4.x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Também ative o acesso `Entitlements.plist` do porta-chaves no ficheiro. Utilize o seguinte grupo de acesso ou o seu próprio grupo de acesso.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Quando utiliza `WithIosKeychainSecurityGroup()` a API, a MSAL ajusta automaticamente o seu grupo de`AppIdentifierPrefix`segurança ao fim do ID da *equipa* da aplicação ( ). A MSAL adiciona o seu grupo de segurança porque quando constrói a sua aplicação em Xcode, fará o mesmo. É por isso que os `Entitlements.plist` direitos `$(AppIdentifierPrefix)` no ficheiro precisam de incluir antes do grupo de acesso à porta-chaves.

Para mais informações, consulte a documentação dos direitos do [iOS.](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps) 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Permitir a partilha de cache simbólica através de aplicações iOS

A partir do MSAL 2.x, pode especificar um grupo de acesso à porta-chaves para persistir a cache simbólica em várias aplicações. Esta definição permite-lhe partilhar a cache simbólica entre várias aplicações que têm o mesmo grupo de acesso à porta-chaves. Pode partilhar o dinheiro simbólico entre [ADAL.NET](https://aka.ms/adal-net) aplicações, MSAL.NET aplicações Xamarin.iOS e aplicações nativas do iOS que foram desenvolvidas em [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Ao partilhar a cache simbólica, permite um único sinal (SSO) entre todas as aplicações que utilizam o mesmo grupo de acesso à porta-chaves.

Para permitir esta partilha `WithIosKeychainSecurityGroup()` de cache, utilize o método para definir o grupo de acesso à cadeia de chaves para o mesmo valor em todas as aplicações que partilham a mesma cache. O primeiro exemplo de código neste artigo mostra como usar o método.

No início deste artigo, descobriu que `$(AppIdentifierPrefix)` a MSAL adiciona sempre que utiliza a `WithIosKeychainSecurityGroup()` API. A MSAL adiciona este `AppIdentifierPrefix` elemento porque o ID da equipa garante que apenas as aplicações que são feitas pela mesma editora podem partilhar o acesso ao keychain.

> [!NOTE]
> A `KeychainSecurityGroup` propriedade está depreciada.
> 
> A partir do MSAL 2.x, os `TeamId` desenvolvedores foram `KeychainSecurityGroup` forçados a incluir o prefixo quando usaram a propriedade. Mas a partir do MSAL 2.7.x, `iOSKeychainSecurityGroup` quando se utiliza a `TeamId` nova propriedade, a MSAL resolve o prefixo durante o tempo de execução. Quando utilizar esta propriedade, não `TeamId` inclua o prefixo no valor. O prefixo não é necessário.
>
> Como `KeychainSecurityGroup` a propriedade é obsoleta, use a `iOSKeychainSecurityGroup` propriedade.

### <a name="use-microsoft-authenticator"></a>Use o autenticador microsoft

A sua aplicação pode utilizar o Microsoft Authenticator como corretor para ativar:

- **SSO:** Quando ativa o SSO, os seus utilizadores não precisam de iniciar sessão em cada aplicação.
- **Identificação do dispositivo**: Utilize a identificação do dispositivo para autenticar acedendo ao certificado do dispositivo. Este certificado é criado no dispositivo quando é ligado ao local de trabalho. A sua aplicação estará pronta se os administradores do arrendatário permitirem o acesso condicional relacionado com os dispositivos.
- **Verificação de identificação de aplicações**: Quando uma aplicação chama o corretor, passa o url de redirecionamento. O corretor verifica o URL de redirecionamento.

Para mais detalhes sobre como ativar um corretor, consulte [Use Microsoft Authenticator ou Microsoft Intune Company Portal em aplicações Xamarin iOS e Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Questões conhecidas com o iOS 12 e autenticação
A Microsoft divulgou um aviso de [segurança](https://github.com/aspnet/AspNetCore/issues/4647) sobre uma incompatibilidade entre o iOS 12 e alguns tipos de autenticação. A incompatibilidade quebra os sign-ins sociais, WSFed e OIDC. O aviso de segurança ajuda os desenvolvedores a entender como remover ASP.NET restrições de segurança das suas aplicações para torná-las compatíveis com o iOS 12.  

Quando desenvolve aplicações MSAL.NET no Xamarin iOS, pode ver um loop infinito quando tenta iniciar sessão em sites do iOS 12. Este comportamento é semelhante a esta [edição da ADAL.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329) 

Também poderá ver uma rutura na autenticação core OIDC ASP.NET com o iOS 12 Safari. Para mais informações, consulte este [problema do WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre propriedades para o xamarin iOS, consulte o parágrafo de [considerações específicas](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) do iOS do ficheiro README.md da amostra seguinte:

Sample | Plataforma | Descrição
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Universal Windows Platform (UWP) | Uma simples aplicação Xamarin Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e Azure AD através do ponto final do Azure AD 2.0. A aplicação também mostra como usar o símbolo resultante para aceder ao Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->