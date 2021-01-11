---
title: Considerações de Xamarin iOS (MSAL.NET) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para a utilização do Xamarin iOS com a Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 62eb4ab9eb6e4b0e7be0f7aadae1173950d21615
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064492"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considerações para a utilização de Xamarin iOS com MSAL.NET

Quando utilizar a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET) no Xamarin iOS, deve:

- Sobrepor e implementar a `OpenUrl` função em `AppDelegate` .
- Ativar grupos de chavechain.
- Ativar a partilha de cache token.
- Ativar o acesso ao chaveiro.
- Compreenda os problemas conhecidos com iOS 12 e iOS 13 e autenticação.

## <a name="implement-openurl"></a>Implementar OpenUrl

Anular o `OpenUrl` método da classe `FormsApplicationDelegate` derivada e chamar `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Eis um exemplo:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Além disso, execute as seguintes tarefas:

* Defina um esquema URI de redirecionamento.
* Requer permissões para que a sua app ligue para outra aplicação.
* Tenha um formulário específico para o URI redirecionado.
* [Registe um URI de redirecionamento](quickstart-register-app.md#add-a-redirect-uri) no portal Azure.

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

A partir de MSAL 2.x, pode especificar um grupo de acesso de chaveiro para persistir a cache simbólica em várias aplicações. Esta definição permite-lhe partilhar a cache simbólica entre várias aplicações que têm o mesmo grupo de acesso ao chaveiro. Pode partilhar a cache simbólica entre [aplicações ADAL.NET,](https://aka.ms/adal-net) aplicações MSAL.NET Xamarin.iOS e aplicações nativas do iOS que foram desenvolvidas em [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Ao partilhar a cache simbólica, permite um único sign-on (SSO) entre todas as aplicações que usam o mesmo grupo de acesso ao chaveiro.

Para permitir esta partilha de cache, utilize o `WithIosKeychainSecurityGroup()` método para definir o grupo de acesso do chaveiro ao mesmo valor em todas as aplicações que partilham a mesma cache. O primeiro exemplo de código neste artigo mostra como utilizar o método.

No início deste artigo, aprendeu que a MSAL adiciona `$(AppIdentifierPrefix)` sempre que utiliza a `WithIosKeychainSecurityGroup()` API. A MSAL adiciona este elemento porque o ID da equipa `AppIdentifierPrefix` garante que apenas as aplicações que são feitas pela mesma editora podem partilhar o acesso ao teclain.

> [!NOTE]
> A `KeychainSecurityGroup` propriedade está prevadida. Use a `iOSKeychainSecurityGroup` propriedade em vez disso. O `TeamId` prefixo não é necessário quando se utiliza `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>Utilize o Autenticador Microsoft

A sua aplicação pode utilizar o Microsoft Authenticator como corretor para ativar:

- **SSO**: Quando ativa o SSO, os seus utilizadores não precisam de iniciar sinas em cada aplicação.
- **Identificação do dispositivo**: Utilize a identificação do dispositivo para autenticar acedendo ao certificado do dispositivo. Este certificado é criado no dispositivo quando é ligado ao local de trabalho. A sua aplicação estará pronta se os administradores do arrendatário permitirem o acesso condicional relacionado com os dispositivos.
- **Verificação de identificação de aplicação**: Quando uma aplicação chama o corretor, passa o seu URL de redirecionamento. O corretor verifica o URL de redirecionamento.

Para obter mais informações sobre como ativar um corretor, consulte [use Microsoft Authenticator ou Microsoft Intune Company Portal em aplicações Xamarin iOS e Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemas conhecidos com iOS 12 e autenticação

A Microsoft lançou um [aviso de segurança](https://github.com/aspnet/AspNetCore/issues/4647) sobre uma incompatibilidade entre o iOS 12 e alguns tipos de autenticação. A incompatibilidade quebra os sign-ins sociais, WSFed e OIDC. O aviso de segurança ajuda-o a entender como remover ASP.NET restrições de segurança das suas aplicações para torná-las compatíveis com o iOS 12.

Quando desenvolves aplicações MSAL.NET no Xamarin iOS, poderás ver um loop infinito quando tentas iniciar sôs-te nos websites do iOS 12. Tal comportamento é semelhante a esta questão ADAL no GitHub: [Infinite loop ao tentar entrar no site a partir do iOS 12 #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329).

Você também pode ver uma quebra na autenticação core OIDC ASP.NET com o iOS 12 Safari. Para mais informações, consulte esta [edição do WebKit.](https://bugs.webkit.org/show_bug.cgi?id=188165)

## <a name="known-issues-with-ios-13-and-authentication"></a>Problemas conhecidos com iOS 13 e autenticação

Se a sua aplicação necessitar de acesso condicional ou suporte à autenticação de certificados, permita que a sua aplicação comunique com a aplicação de corretor microsoft Authenticator. A MSAL é então responsável pelo tratamento de pedidos e respostas entre a sua aplicação e o Microsoft Authenticator.

No iOS 13, a Apple fez uma alteração de API de rutura removendo a capacidade da aplicação de ler a aplicação de origem ao receber uma resposta de uma aplicação externa através de esquemas de URL personalizados.

A documentação da Apple para [o UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) afirma:

> *Se o pedido tiver origem noutra app pertencente à sua equipa, o UIKit define o valor desta chave para o ID dessa aplicação. Se o identificador de equipa da app originária for diferente do identificador de equipa da aplicação atual, o valor da chave é nulo.*

Esta alteração está a ser quebrada para a MSAL porque dependia `UIApplication.SharedApplication.OpenUrl` de verificar a comunicação entre a MSAL e a app Microsoft Authenticator.

Além disso, no iOS 13, o desenvolvedor é obrigado a fornecer um controlador de apresentação durante a utilização `ASWebAuthenticationSession` .

A sua aplicação é impactada se estiver a construir com o Xcode 11 e utilizar um corretor iOS ou `ASWebAuthenticationSession` .

Nestes casos, utilize [MSAL.NET 4.4.0+](https://www.nuget.org/packages/Microsoft.Identity.Client/) para permitir a autenticação com sucesso.

### <a name="additional-requirements"></a>Requisitos adicionais

- Ao utilizar as mais recentes bibliotecas MSAL, certifique-se de que a **versão 6.3.19+** do Microsoft Authenticator está instalada no dispositivo.
- Ao atualizar para MSAL.NET 4.4.0+, atualize o seu `LSApplicationQueriesSchemes` no ficheiro *Info.plist* e `msauthv3` adicione:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    A adição `msauthv3` ao *Info.plist* é necessária para detetar a presença da mais recente aplicação autenticadora da Microsoft no dispositivo que suporta o iOS 13.

## <a name="report-an-issue"></a>Comunicar um problema

Se tiver dúvidas ou quiser reportar um problema que encontrou no MSAL.NET, abra um problema no repositório [AzureAD/microsoft-authentication-library-for-dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) no GitHub.

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre propriedades para xamarin iOS, consulte o parágrafo de [considerações específicas](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) para o iOS do ficheiro README.md da amostra seguinte:

Sample | Plataforma | Descrição
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Universal Windows Platform (UWP) | Uma aplicação simples Xamarin Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e AD AD AZure através do ponto final Azure AD 2.0. A aplicação também mostra como usar o token resultante para aceder ao Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
