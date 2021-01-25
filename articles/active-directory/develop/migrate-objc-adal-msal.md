---
title: Guia de migração ADAL para MSAL (MSAL iOS/macOS) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba as diferenças entre o MSAL para iOS/macOS e a Biblioteca de Autenticação AD Azure para o ObjectiveC (ADAL). ObjC) e como migrar para MSAL para iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 7dc3241198fbc6eeddba059251f28c6dc35c8a29
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754941"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrar aplicações para MSAL para iOS e macOS

A Azure Ative Directory Authentication Library[(ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) foi criada para trabalhar com contas do Azure Ative Directory através do ponto final v1.0.

A Microsoft Authentication Library for iOS and macOS (MSAL) foi construída para funcionar com todas as identidades da Microsoft, tais como contas Azure Ative Directory (Azure AD), contas pessoais da Microsoft e contas AZure AD B2C através da plataforma de identidade microsoft (formalmente o Azure AD v2.0 endpoint).

A plataforma de identidade da Microsoft tem algumas diferenças fundamentais com o Azure Ative Directory v1.0. Este artigo destaca estas diferenças e fornece orientações para migrar uma aplicação de ADAL para MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Diferenças de capacidade de app ADAL e MSAL

### <a name="who-can-sign-in"></a>Quem pode entrar

* A ADAL só suporta contas de trabalho e escolas- também conhecidas como contas AZURE AD.
* A MSAL suporta contas pessoais da Microsoft (contas MSA) como Hotmail.com, Outlook.com e Live.com.
* A MSAL apoia contas de trabalho e escolas, e contas Azure AD B2C.

### <a name="standards-compliance"></a>Cumprimento das normas

* A plataforma de identidade da Microsoft segue os padrões OAuth 2.0 e OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

* O Azure Ative Directory v1.0 endpoint exige que todas as permissões sejam declaradas antecipadamente durante o registo da aplicação. Isto significa que as permissões são estáticas.
* A plataforma de identidade da Microsoft permite-lhe solicitar permissões dinamicamente. As aplicações podem pedir permissões apenas quando necessário e pedir mais à medida que a app precisa delas.

Para saber mais sobre as diferenças entre o Azure Ative Directory v1.0 e a plataforma de identidade da Microsoft, consulte [porquê a atualização para a plataforma de identidade da Microsoft?](../azuread-dev/azure-ad-endpoint-comparison.md)

## <a name="adal-and-msal-library-differences"></a>Diferenças na biblioteca ADAL e MSAL

A API pública MSAL reflete algumas diferenças fundamentais entre o Azure AD v1.0 e a plataforma de identidade microsoft.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicCientApplication em vez de ADAuthenticationContexto

`ADAuthenticationContext` é o primeiro objeto que uma aplicação ADAL cria. Representa uma instantânea de ADAL. As aplicações criam um novo exemplo `ADAuthenticationContext` de cada combinação de cloud e inquilino (autoridade) do Azure Ative Directory. O mesmo `ADAuthenticationContext` pode ser usado para obter fichas para várias aplicações de clientes públicos.

No MSAL, a principal interação é através de um `MSALPublicClientApplication` objeto, que é modelado após [OAuth 2.0 Cliente Público](https://tools.ietf.org/html/rfc6749#section-2.1). Um exemplo `MSALPublicClientApplication` pode ser usado para interagir com várias nuvens de AAD, e inquilinos, sem necessidade de criar um novo exemplo para cada autoridade. Para a maioria das aplicações, um `MSALPublicClientApplication` caso é suficiente.

### <a name="scopes-instead-of-resources"></a>Âmbitos em vez de recursos

Na ADAL, uma aplicação teve de fornecer um identificador de *recursos* como `https://graph.microsoft.com` adquirir fichas do Azure Ative Directory v1.0. Um recurso pode definir uma série de âmbitos, ou oAuth2Permissions no manifesto da aplicação, que compreende. Isto permitiu que as aplicações do cliente solicitassem fichas desse recurso para um determinado conjunto de âmbitos pré-definidos durante o registo da aplicação.

No MSAL, em vez de um único identificador de recursos, as aplicações fornecem um conjunto de âmbitos por pedido. Um âmbito é um identificador de recursos seguido de um nome de permissão no formulário recurso/permissão. Por exemplo, `https://graph.microsoft.com/user.read`

Existem duas formas de fornecer âmbitos no MSAL:

* Forneça uma lista de todas as permissões que as suas aplicações precisam. Por exemplo: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    Neste caso, a aplicação solicita as `directory.read` `directory.write` permissões e permissões. O utilizador será solicitado a consentir essas permissões se não tiver consentido antes para esta aplicação. A aplicação poderá também receber permissões adicionais que o utilizador já tenha consentido para a aplicação. O utilizador só será solicitado a consentir novas permissões ou permissões que não tenham sido concedidas.

* O `/.default` âmbito.

Este é o espaço incorporado para cada aplicação. Refere-se à lista estática de permissões configuradas quando o pedido foi registado. O seu comportamento é semelhante ao `resource` de. Isto pode ser útil ao migrar para garantir que um conjunto semelhante de âmbitos e experiência do utilizador é mantido.

Para utilizar o `/.default` âmbito, apê-lo `/.default` ao identificador de recursos. Por exemplo: `https://graph.microsoft.com/.default`. Se o seu recurso terminar com um corte `/` (), deve ainda apendê-lo, `/.default` incluindo o corte dianteiro principal, resultando num âmbito que tenha um corte duplo para a frente ( `//` ) nele.

Pode ler mais informações sobre a utilização do âmbito "/.predefinição" [aqui](./v2-permissions-and-consent.md#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Suportando diferentes tipos de WebView & navegadores

A ADAL suporta apenas uIWebView/WKWebView para iOS e WebView para macOS. O MSAL para iOS suporta mais opções para exibir conteúdo web ao solicitar um código de autorização, e já não suporta `UIWebView` ; o que pode melhorar a experiência e segurança do utilizador.

Por padrão, o MSAL no iOS utiliza [o ASWebAuthenticationSsion](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), que é o componente web que a Apple recomenda para autenticação em dispositivos iOS 12+. Ele fornece benefícios de Sign-On (SSO) através da partilha de cookies entre apps e o navegador Safari.

Pode optar por utilizar um componente web diferente, dependendo dos requisitos da aplicação e da experiência do utilizador final que pretende. Consulte [os tipos de visualização web suportados](customize-webviews.md) para obter mais opções.

Ao migrar de ADAL para MSAL, `WKWebView` proporciona a experiência do utilizador mais semelhante à ADAL no iOS e no macOS. Encorajamo-lo a migrar para `ASWebAuthenticationSession` o iOS, se possível. Para o macOS, encorajamo-lo a `WKWebView` usar.

### <a name="account-management-api-differences"></a>Diferenças de API de gestão de conta

Quando liga para os métodos ADAL `acquireToken()` `acquireTokenSilent()` ou, recebe um `ADUserInformation` objeto que contém uma lista de reclamações do que representa a `id_token` autenticação da conta. Além disso, `ADUserInformation` devolve um com base na `userId` `upn` reclamação. Após a aquisição inicial de token interativo, a ADAL espera que o desenvolvedor forneça `userId` em todas as chamadas silenciosas.

A ADAL não fornece uma API para recuperar identidades conhecidas dos utilizadores. Conta com a app para guardar e gerir essas contas.

A MSAL fornece um conjunto de APIs para listar todas as contas conhecidas pela MSAL sem ter de adquirir um token.

Tal como a ADAL, a MSAL devolve informações sobre contas que detenha uma lista de reclamações do `id_token` . É parte do `MSALAccount` objeto dentro do `MSALResult` objeto.

A MSAL fornece um conjunto de APIs para remover contas, tornando as contas removidas inacessíveis à aplicação. Após a remoção da conta, as chamadas de aquisição posteriores de token levarão o utilizador a fazer a aquisição de fichas interativas. A remoção da conta aplica-se apenas à aplicação do cliente que a iniciou, e não remove a conta das outras aplicações em execução no dispositivo ou do navegador do sistema. Isto garante que o utilizador continua a ter uma experiência SSO no dispositivo mesmo depois de ter assinado uma aplicação individual.

Além disso, a MSAL também devolve um identificador de conta que pode ser usado para solicitar um token silenciosamente mais tarde. No entanto, o identificador de conta (acessível através `identifier` de propriedade no `MSALAccount` objeto) não é exibivel e não pode assumir em que formato se encontra, nem deve tentar interpretá-lo ou analisá-lo.

### <a name="migrating-the-account-cache"></a>Migração da cache da conta

Ao migrar da ADAL, as aplicações normalmente armazenam ADAL's `userId` , que não tem o exigido pela `identifier` MSAL. Como um passo de migração único, uma aplicação pode consultar uma conta MSAL usando o userId da ADAL com a seguinte API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Esta API lê a cache da MSAL e da ADAL para encontrar a conta pelo utilizador ADALId (UPN).

Se a conta for encontrada, o desenvolvedor deve usar a conta para fazer a aquisição de token silencioso. A primeira aquisição de token silencioso irá efetivamente atualizar a conta, e o desenvolvedor obterá um identificador de conta compatível com MSAL no resultado MSAL ( `identifier` ). Depois disso, só `identifier` deve ser utilizado para procura de conta utilizando a seguinte API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Embora seja possível continuar a usar ADAL's `userId` para todas as operações em MSAL, uma vez `userId` que é baseado na UPN, está sujeito a múltiplas limitações que resultam numa má experiência do utilizador. Por exemplo, se a UPN mudar, o utilizador tem de voltar a iniciar sôm. Recomendamos que todas as aplicações utilizem a conta não exibiível `identifier` para todas as operações.

Leia mais sobre [a migração do estado de cache.](sso-between-adal-msal-apps-macos-ios.md)

### <a name="token-acquisition-changes"></a>Alterações de aquisição de Token

A MSAL introduz algumas alterações na chamada de aquisição simbólicas:

* Como a ADAL, `acquireTokenSilent` resulta sempre num pedido silencioso.
* Ao contrário do ADAL, `acquireToken` resulta sempre em UI accível pelo utilizador, quer através da visualização web, quer da aplicação Microsoft Authenticator. Dependendo do estado SSO dentro do webview/Microsoft Authenticator, o utilizador pode ser solicitado a introduzir as suas credenciais.
* Em ADAL, `acquireToken` com primeira tentativa silenciosa de aquisição de `AD_PROMPT_AUTO` token, e só mostra UI se o pedido silencioso falhar. No MSAL, esta lógica pode ser alcançada através da primeira chamada `acquireTokenSilent` e da chamada `acquireToken` apenas se a aquisição silenciosa falhar. Isto permite que os desenvolvedores personalizem a experiência do utilizador antes de iniciarem a aquisição de tokens interativos.

### <a name="error-handling-differences"></a>Diferenças de manipulação de erros

A MSAL fornece mais clareza entre os erros que podem ser tratados pela sua app e aqueles que requerem intervenção do utilizador. Há um número limitado de erros que o desenvolvedor deve lidar:

* `MSALErrorInteractionRequired`: O utilizador deve fazer um pedido interativo. Isto pode ser causado por vários motivos, como uma sessão de autenticação expirada, a política de acesso condicional mudou, um token de atualização expirou ou foi revogado, não há fichas válidas na cache, e assim por diante.
* `MSALErrorServerDeclinedScopes`: O pedido não foi totalmente concluído e alguns âmbitos não tiveram acesso. Isto pode ser causado por um utilizador declinando o consentimento para um ou mais âmbitos.

Lidar com todos os outros erros da [ `MSALError` lista](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) é opcional. Pode utilizar a informação nesses erros para melhorar a experiência do utilizador.

Consulte [exceções e erros de manuseamento utilizando o MSAL](msal-error-handling-ios.md) para obter mais informações sobre o tratamento de erros da MSAL.

### <a name="broker-support"></a>Suporte a corretor

O MSAL, a começar pela versão 0.3.0, fornece suporte para autenticação intermediada utilizando a aplicação Microsoft Authenticator. O Microsoft Authenticator também permite o suporte para cenários de Acesso Condicional. Exemplos de cenários de Acesso Condicional incluem políticas de conformidade do dispositivo que exigem que o utilizador inscreva o dispositivo através do Intune ou registe-se com a AAD para obter um token. E políticas de Gestão de Aplicações Móveis (MAM) de Acesso Condicionado, que requerem uma prova de conformidade antes de a sua aplicação poder obter um token.

Para ativar a sua aplicação:

1. Registe um formato URI de redirecionamento compatível com corretor para a aplicação. O formato URI de redirecionamento compatível com corretor é `msauth.<app.bundle.id>://auth` . `<app.bundle.id>`Substitua-o pelo ID do feixe da sua aplicação. Se estás a migrar da ADAL e a tua candidatura já era capaz de for broker, não há nada a mais que precises de fazer. O seu URI de redirecionamento anterior é totalmente compatível com o MSAL, para que possa saltar para o passo 3.

2. Adicione o esquema URI de redirecionamento da sua aplicação ao seu ficheiro info.plist. Para o MSAL padrão redirecionar URI, o formato é `msauth.<app.bundle.id>` . Por exemplo:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Adicione os seguintes esquemas à info.plist da sua aplicação ao abrigo do LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Adicione o seguinte ao seu ficheiro .m AppDeegate para lidar com chamadas: Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Rápido:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Empresa-empresa (B2B)

Na ADAL, cria-se instâncias separadas `ADAuthenticationContext` de cada inquilino que a app pede fichas. Isto já não é um requisito na MSAL. No MSAL, pode criar uma única instância `MSALPublicClientApplication` e usá-la para qualquer nuvem e organização AAD, especificando uma autoridade diferente para adquirirToken e adquirir chamadasTokenSilent.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO em parceria com outros SDKs

MSAL para iOS pode alcançar SSO através de uma cache unificada com os seguintes SDKs:

- ADAL Objective-C 2.7.x+
- MSAL.NET para Xamarin 2.4.x+
- ADAL.NET para Xamarin 4.4.x+

O SSO é alcançado através da partilha de chaveiros iOS e só está disponível entre aplicações publicadas a partir da mesma conta Apple Developer.

SSO através da partilha de chaveiros iOS é o único tipo SSO silencioso.

No que diz sobre o macOS, o MSAL pode alcançar SSO com outros MSAL para aplicações baseadas em iOS e macOS e aplicações baseadas em ADAL Objective-C.

MSAL no iOS também suporta dois outros tipos de SSO:

* SSO através do navegador web. O MSAL para suportes iOS `ASWebAuthenticationSession` , que fornece SSO através de cookies partilhados entre outras aplicações no dispositivo e especificamente o navegador Safari.
* SSO através de um corretor de autenticação. Num dispositivo iOS, o Microsoft Authenticator atua como o corretor de autenticação. Pode seguir políticas de Acesso Condicional, tais como a necessidade de um dispositivo em conformidade, e fornece SSO para dispositivos registados. Os MSAL SDKs começam com a versão 0.3.0 suportam um corretor por padrão.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

O [Intune MAM SDK](/intune/app-sdk-get-started) suporta o MSAL para iOS a partir da versão [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL e ADAL na mesma app

A versão ADAL 2.7.0, e acima, não pode coexistir com a MSAL na mesma aplicação. A razão principal é devido ao código comum submodule partilhado. Como o Objective-C não suporta espaços de nome, se adicionar tanto os quadros ADAL como o MSAL à sua aplicação, haverá dois casos da mesma classe. Não há garantias para qual é escolhido em tempo de execução. Se ambos os SDKs estiverem a usar a mesma versão da classe conflituosa, a sua aplicação ainda pode funcionar. No entanto, se for uma versão diferente, a sua app poderá sofrer falhas inesperadas que são difíceis de diagnosticar.

Correr a ADAL e a MSAL na mesma aplicação de produção não é suportado. No entanto, se estiver apenas a testar e migrar os seus utilizadores de ADAL Objective-C para MSAL para iOS e macOS, pode continuar a utilizar [o ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). É a única versão que funciona com a MSAL na mesma aplicação. Não haverá novas atualizações de funcionalidades para esta versão ADAL, pelo que esta deve ser utilizada apenas para fins de migração e testes. A sua aplicação não deve contar com a coexistência ADAL e MSAL a longo prazo.

A coexistência da ADAL e msal na mesma aplicação não é apoiada.
A coexistência ADAL e MSAL entre múltiplas aplicações é totalmente suportada.

## <a name="practical-migration-steps"></a>Etapas de migração práticas

### <a name="app-registration-migration"></a>Migração de registo de aplicativos

Não precisa de alterar a sua aplicação AAD existente para mudar para MSAL e ativar as contas AAD. No entanto, se a sua aplicação baseada em ADAL não suportar a autenticação intermediada, terá de registar um novo URI de redirecionamento para a aplicação antes de poder mudar para MSAL.

O URI de redirecionamento deve estar neste formato: `msauth.<app.bundle.id>://auth` . `<app.bundle.id>`Substitua-o pelo ID do feixe da sua aplicação. Especifique o URI de redirecionamento no [portal Azure](https://aka.ms/MobileAppReg).

Apenas para iOS, para suportar a autenticação baseada em cert, um retoque adicional URI precisa de ser registado na sua aplicação e no portal Azure no seguinte formato: `msauth://code/<broker-redirect-uri-in-url-encoded-form>` . Por exemplo, `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Recomendamos que todas as aplicações registem ambos os URIs redirecionados.

Se pretender adicionar suporte para consentimento incremental, selecione as APIs e as permissões a que a sua aplicação está configurada para solicitar acesso no registo da sua aplicação no separador **permissões API.**

Se você está migrando da ADAL e quer suportar as contas AAD e MSA, o seu registo de aplicação existente precisa ser atualizado para suportar ambos. Não recomendamos que atualize a sua app de produção existente para suportar de imediato tanto a AAD como a MSA. Em vez disso, crie outro ID de cliente que suporte tanto a AAD como a MSA para testes, e depois de verificar que todos os cenários funcionam, atualize a app existente.

### <a name="add-msal-to-your-app"></a>Adicione MSAL à sua app

Pode adicionar MSAL SDK à sua aplicação utilizando a sua ferramenta de gestão de pacotes preferida. Consulte [instruções detalhadas aqui.](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)

### <a name="update-your-apps-infoplist-file"></a>Atualize o ficheiro Info.plist da sua aplicação

Apenas para iOS, adicione o esquema URI redirecionado da sua aplicação ao seu ficheiro info.plist. Para aplicações compatíveis com corretores ADAL, já deve estar lá. O sistema URI de redirecionamento padrão MSAL estará no formato: `msauth.<app.bundle.id>` .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Adicione os seguintes esquemas à info.plist da sua aplicação em `LSApplicationQueriesSchemes` .

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Atualize o seu código AppDeegate

Apenas para iOS, adicione o seguinte ao seu ficheiro .m AppDeegate:

Objetivo C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Rápido:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Se estiver a utilizar o Xcode 11,** deverá colocar a chamada MSAL no `SceneDelegate` ficheiro.
Se apoiar tanto o UISceneDeeDeegate como o UIApplicationDeegate para compatibilidade com iOS mais antigo, o callback MSAL teria de ser colocado em ambos os ficheiros.

Objetivo C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Rápido:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Isto permite que a MSAL lide com as respostas do corretor e componente web.
Isto não era necessário na ADAL, uma vez que "swizzed" métodos de delegado de aplicações automaticamente. Adicioná-lo manualmente é menos propenso a erros e dá mais controlo à aplicação.

### <a name="enable-token-caching"></a>Permitir caching token

Por predefinição, o MSAL caches os tokens da sua aplicação no chaveiro iOS ou macOS. 

Para permitir o caching token:
1. Certifique-se de que a sua candidatura está devidamente assinada
2. Aceda ao seu separador de Definições de Projeto Xcode > **Capacidades**  >  **Ativar a partilha de chavechains**
3. Clique **+** e introduza uma seguinte entrada **dos Grupos Keychain:** 3.a Para iOS, introduza `com.microsoft.adalcache` 3.b Para introduzir o macOS `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Crie MSALPublicClientApplication e mude para a sua aquisiçãoToken e adquira chamadasTokeSilent

Pode criar `MSALPublicClientApplication` usando o seguinte código:

Objetivo C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Rápido:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Em seguida, ligue para a API de gestão de conta para ver se há alguma conta na cache:

Objetivo C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Rápido:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



ou ler todas as contas:

Objetivo C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Rápido:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Se for encontrada uma conta, ligue para a `acquireTokenSilent` API MSAL:

Objetivo C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Rápido:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)