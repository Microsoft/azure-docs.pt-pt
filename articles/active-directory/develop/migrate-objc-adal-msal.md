---
title: ADAL ao guia de migração MSAL (MSAL iOS/macOS)  Azure
titleSuffix: Microsoft identity platform
description: Conheça as diferenças entre o MSAL para iOS/macOS e a Biblioteca de Autenticação AD Azure para o ObjectiveC (ADAL). ObjC) e como migrar para mSAL para iOS/macOS.
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
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085175"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrar aplicações para o MSAL para iOS e macOS

A Biblioteca de Autenticação de Diretórios Ativos Azure ([ADAL Objective-C)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)foi criada para trabalhar com contas azure Ative Directory através do ponto final v1.0.

A Microsoft Authentication Library para iOS e macOS (MSAL) foi construída para trabalhar com todas as identidades da Microsoft, como contas Azure Ative Directory (Azure AD), contas pessoais da Microsoft e contas Azure AD B2C através da plataforma de identidade da Microsoft (formalmente o ponto final azure AD v2.0).

A plataforma de identidade da Microsoft tem algumas diferenças fundamentais com o Azure Ative Directory v1.0. Este artigo destaca estas diferenças e fornece orientação para migrar uma aplicação da ADAL para a MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Diferenças de capacidade de aplicação ADAL e MSAL

### <a name="who-can-sign-in"></a>Quem pode assinar

* A ADAL apenas apoia o trabalho e as contas escolares-- também conhecidas como contas Azure AD.
* A MSAL suporta contas pessoais da Microsoft (contas MSA) como Hotmail.com, Outlook.com e Live.com.
* A MSAL apoia as contas de trabalho e escola, e contas Azure AD B2C.

### <a name="standards-compliance"></a>Conformidade de normas

* O ponto final da Plataforma de Identidade da Microsoft segue os padrões OAuth 2.0 e OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

* O ponto final do Diretório Ativo Azure v1.0 exige que todas as permissões sejam declaradas antecipadamente durante o registo da candidatura. Isto significa que as permissões são estáticas.
* A plataforma de identidade da Microsoft permite-lhe solicitar permissões de forma dinâmica. As aplicações podem pedir permissões apenas se necessário e pedir mais como a aplicação precisa delas.

Para saber mais sobre as diferenças entre o Azure Ative Directory v1.0 e a plataforma de identidade da Microsoft, consulte [porquê atualizar para a plataforma de identidade da Microsoft (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="adal-and-msal-library-differences"></a>Diferenças na biblioteca ADAL e MSAL

A API pública mSAL reflete algumas diferenças fundamentais entre o Azure AD v1.0 e a plataforma de identidade da Microsoft.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication em vez de ADAuthenticationContext

`ADAuthenticationContext` é o primeiro objeto que uma aplicação ADAL cria. Representa uma instantânea da ADAL. As aplicações criam uma nova instância de `ADAuthenticationContext` para cada nuvem de diretório Ativo Azure e combinação de inquilinos (autoridade). O mesmo `ADAuthenticationContext` pode ser usado para obter fichas para múltiplas aplicações de clientes públicos.

Na MSAL, a principal interação é através de um objeto `MSALPublicClientApplication`, que é modelado após [o OAuth 2.0 Cliente Público.](https://tools.ietf.org/html/rfc6749#section-2.1) Um caso de `MSALPublicClientApplication` pode ser usado para interagir com várias nuvens aAD, e inquilinos, sem necessidade de criar um novo exemplo para cada autoridade. Para a maioria das aplicações, um `MSALPublicClientApplication` instância é suficiente.

### <a name="scopes-instead-of-resources"></a>Âmbitos em vez de recursos

Na ADAL, uma aplicação teve de fornecer um identificador de *recursos* como `https://graph.microsoft.com` adquirir fichas do Azure Ative Directory v1.0 endpoint. Um recurso pode definir uma série de âmbitos, ou oAuth2Permissions no manifesto da aplicação, que compreende. Isto permitiu que as aplicações dos clientes solicitassem fichas desse recurso para um determinado conjunto de âmbitos pré-definidos durante o registo da aplicação.

Na MSAL, em vez de um único identificador de recursos, as aplicações fornecem um conjunto de âmbitos por pedido. Um âmbito é um identificador de recursos seguido de um nome de permissão no formulário recurso/permissão. Por exemplo, `https://graph.microsoft.com/user.read`

Existem duas formas de fornecer âmbitos no MSAL:

* Forneça uma lista de todas as permissões que as suas apps precisam. Por exemplo: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    Neste caso, a aplicação solicita as permissões `directory.read` e `directory.write`. O utilizador será solicitado a consentir com essas permissões se não tiver consentido com elas antes para esta aplicação. A aplicação poderá ainda receber permissões adicionais que o utilizador já consentiu para a aplicação. O utilizador só será solicitado a consentir com novas permissões ou permissões que não tenham sido concedidas.

* O âmbito `/.default`.

Esta é a mira incorporada para cada aplicação. Refere-se à lista estática de permissões configuradas quando o pedido foi registado. O seu comportamento é semelhante ao da `resource`. Isto pode ser útil quando se migra para garantir que um conjunto semelhante de âmbitos e experiência do utilizador seja mantido.

Para utilizar o âmbito `/.default`, apreenda `/.default` ao identificador de recursos. Por exemplo: `https://graph.microsoft.com/.default`. Se o seu recurso terminar com um corte (`/`), deve ainda anexar `/.default`, incluindo o principal corte dianteiro, resultando num âmbito que tem um duplo corte para a frente (`//`) nele.

Pode ler mais informações sobre a utilização do âmbito "/.default" [aqui](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Suportando diferentes tipos e navegadores WebView

O ADAL apenas suporta UIWebView/WKWebView para iOS e WebView para macOS. A MSAL para o iOS suporta mais opções para exibir conteúdos web ao solicitar um código de autorização, e já não suporta `UIWebView`; que pode melhorar a experiência e a segurança do utilizador.

Por padrão, o MSAL no iOS utiliza o [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), que é o componente web que a Apple recomenda para autenticação em dispositivos iOS 12+. Fornece benefícios de Sign-On Único (SSO) através da partilha de cookies entre apps e o navegador Safari.

Pode optar por utilizar um componente web diferente, dependendo dos requisitos da aplicação e da experiência de utilizador final que deseja. Consulte tipos de [visualização web suportados](customize-webviews.md) para mais opções.

Ao migrar de ADAL para MSAL, `WKWebView` fornece a experiência do utilizador mais semelhante à ADAL no iOS e macOS. Encorajamo-lo a migrar para `ASWebAuthenticationSession` no iOS, se possível. Para o macOS, encorajamo-lo a usá-lo `WKWebView`.

### <a name="account-management-api-differences"></a>Diferenças de API de gestão de conta

Quando liga para os métodos ADAL `acquireToken()` ou `acquireTokenSilent()`, recebe um objeto `ADUserInformation` contendo uma lista de reclamações do `id_token` que representa a autenticação da conta. Além disso, `ADUserInformation` devolve um `userId` com base na alegação `upn`. Após a aquisição inicial de token interativo, a ADAL espera que o desenvolvedor forneça `userId` em todas as chamadas silenciosas.

A ADAL não fornece uma API para recuperar identidades conhecidas do utilizador. Conta com a app para guardar e gerir essas contas.

A MSAL fornece um conjunto de APIs para listar todas as contas conhecidas pela MSAL sem ter que adquirir um símbolo.

Tal como a ADAL, a MSAL devolve informações de conta que detêm uma lista de reclamações do `id_token`. Faz parte do objeto `MSALAccount` dentro do objeto `MSALResult`.

A MSAL fornece um conjunto de APIs para remover contas, tornando as contas removidas inacessíveis à app. Após a remoção da conta, chamadas de aquisição de token posteriores levarão o utilizador a fazer aquisição interativa de token. A remoção da conta aplica-se apenas à aplicação do cliente que a iniciou, e não remove a conta das outras aplicações que estão a funcionar no dispositivo ou no navegador do sistema. Isto garante que o utilizador continua a ter uma experiência SSO no dispositivo mesmo depois de assinar uma aplicação individual.

Além disso, a MSAL também devolve um identificador de conta que pode ser usado para solicitar um token silenciosamente mais tarde. No entanto, o identificador de conta (acessível através de `identifier` propriedade no objeto `MSALAccount`) não é apresentável e não pode assumir em que formato se encontra nem deve tentar interpretá-lo ou analisá-lo.

### <a name="migrating-the-account-cache"></a>Migração do cache da conta

Ao migrar da ADAL, as aplicações normalmente armazenam o `userId`da ADAL, que não tem a `identifier` exigida pela MSAL. Como um passo de migração único, uma aplicação pode consultar uma conta MSAL usando o userId da ADAL com a seguinte API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Esta API lê tanto a cache da MSAL como da ADAL para encontrar a conta por ID (UPN) da ADAL.

Se a conta for encontrada, o desenvolvedor deve usar a conta para fazer a aquisição de fichas silenciosas. A primeira aquisição de token silencioso irá efetivamente atualizar a conta, e o desenvolvedor receberá um identificador de conta compatível com MSAL no resultado do MSAL (`identifier`). Depois disso, só `identifier` devem ser utilizados para a procura de conta utilizando a seguinte API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Embora seja possível continuar a usar o `userId` da ADAL para todas as operações na MSAL, uma vez que `userId` é baseado na UPN, está sujeito a múltiplas limitações que resultam numa má experiência do utilizador. Por exemplo, se a UPN mudar, o utilizador tem de voltar a fazer o seu insessão. Recomendamos que todas as aplicações utilizem a conta não apresentável `identifier` para todas as operações.

Leia mais sobre a [migração do estado cache](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Alterações na aquisição de token

A MSAL introduz algumas alterações de chamadas de aquisição simbólicas:

* Tal como a ADAL, `acquireTokenSilent` sempre resulta num pedido silencioso.
* Ao contrário do ADAL, `acquireToken` sempre resulta em UI acionável pelo utilizador, quer através da visão web, quer da aplicação Microsoft Authenticator. Dependendo do estado SSO dentro do webview/Microsoft Authenticator, o utilizador pode ser solicitado a introduzir as suas credenciais.
* Na ADAL, `acquireToken` com `AD_PROMPT_AUTO` primeira tentativa de aquisição de token silenciosa, e só mostra UI se o pedido silencioso falhar. Na MSAL, esta lógica pode ser alcançada através da primeira chamada `acquireTokenSilent` e apenas chamando `acquireToken` se a aquisição silenciosa falhar. Isto permite que os desenvolvedores personalizem a experiência do utilizador antes de iniciar em aquisição interativa de token.

### <a name="error-handling-differences"></a>Diferenças de manipulação de erros

A MSAL fornece mais clareza entre os erros que podem ser tratados pela sua app e os que requerem a intervenção do utilizador. Há um número limitado de erros que o desenvolvedor deve lidar:

* `MSALErrorInteractionRequired`: O utilizador deve fazer um pedido interativo. Isto pode ser causado por várias razões como uma sessão de autenticação expirada, a política de Acesso Condicional mudou, um token de atualização expirou ou foi revogado, não há fichas válidas na cache, e assim por diante.
* `MSALErrorServerDeclinedScopes`: O pedido não foi totalmente concluído e alguns âmbitos não tiveram acesso. Isto pode ser causado por um utilizador que declina o consentimento para um ou mais âmbitos.

Lidar com todos os outros erros na [lista de`MSALError`](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) é opcional. Pode utilizar a informação nesses erros para melhorar a experiência do utilizador.

Consulte as exceções e erros de manuseamento utilizando o [MSAL](msal-handling-exceptions.md) para obter mais informações sobre o manuseamento de erros MSAL.

### <a name="broker-support"></a>Suporte de corretor

A MSAL, a começar pela versão 0.3.0, fornece suporte para autenticação intermediada utilizando a aplicação Microsoft Authenticator. O Microsoft Authenticator também permite suporte para cenários de Acesso Condicional. Exemplos de cenários de Acesso Condicional incluem políticas de conformidade do dispositivo que exigem que o utilizador inscreva o dispositivo através de Intune ou se registe com a AAD para obter um sinal. E políticas de Acesso Condicional de Aplicações Móveis (MAM), que requerem prova de conformidade antes que a sua aplicação possa obter um sinal.

Para ativar o corretor para a sua aplicação:

1. Registe um formato URI compatível com um corretor para a aplicação. O formato URI compatível com o corretor é `msauth.<app.bundle.id>://auth`. Substitua `<app.bundle.id>` com o pacote de identificação da sua aplicação. Se está a migrar da ADAL e a sua aplicação já era capaz de intermediar, não há nada a mais que precise de fazer. O seu URI redirecionado anterior é totalmente compatível com mSAL, para que possa saltar para o passo 3.

2. Adicione o sistema URI redirecionamento da sua aplicação ao seu ficheiro info.plist. Para o redirecionamento padrão mSAL URI, o formato é `msauth.<app.bundle.id>`. Por exemplo:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Adicione os seguintes esquemas à info.plist da sua aplicação no lSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Adicione o seguinte ao seu ficheiro AppDelegate.m para lidar com chamadas: Objectivo-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Negócios a empresas (B2B)

Na ADAL, cria-se casos separados de `ADAuthenticationContext` para cada inquilino que a aplicação solicita tokens. Isto já não é um requisito na MSAL. Em MSAL, você pode criar uma única instância de `MSALPublicClientApplication` e usá-lo para qualquer nuvem e organização AAD, especificando uma autoridade diferente para adquirir chamadasToken e adquirir Chamadas TokenSilent.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO em parceria com outros SDKs

A MSAL para iOS pode alcançar SSO através de uma cache unificada com os seguintes SDKs:

- ADAL Objectivo-C 2.7.x+
- MSAL.NET para Xamarin 2.4.x+
- ADAL.NET para Xamarin 4.4.x+

O SSO é conseguido através da partilha de keychain iOS e só está disponível entre aplicações publicadas na mesma conta apple Developer.

A partilha de porta-chaves iOS é o único tipo sSO silencioso.

No macOS, o MSAL pode alcançar SSO com outros MSAL para aplicações baseadas em iOS e macOS e aplicações baseadas em Objectivo-C da ADAL.

A MSAL no iOS também suporta dois outros tipos de SSO:

* SSO através do navegador web. A MSAL para suportes iOS `ASWebAuthenticationSession`, que fornece SSO através de cookies partilhados entre outras aplicações no dispositivo e especificamente o navegador Safari.
* SSO através de um corretor de autenticação. Num dispositivo iOS, o Microsoft Authenticator funciona como o corretor de Autenticação. Pode seguir políticas de Acesso Condicional, tais como a necessidade de um dispositivo compatível, e fornece SSO para dispositivos registados. Os SDKs MSAL a partir da versão 0.3.0 suportam um corretor por defeito.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

O [Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) suporta mSAL para iOS a partir da versão [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL e ADAL na mesma app

A versão ADAL 2.7.0, e acima, não pode coexistir com a MSAL na mesma aplicação. A razão principal é por causa do código comum do submódulo partilhado. Como o Objective-C não suporta espaços de nome, se adicionar os quadros ADAL e MSAL à sua aplicação, haverá dois casos da mesma classe. Não há garantias para qual é escolhido em tempo de execução. Se ambos os SDKs estiverem a usar a mesma versão da classe conflituosa, a sua aplicação ainda pode funcionar. No entanto, se for uma versão diferente, a sua aplicação poderá sofrer falhas inesperadas que são difíceis de diagnosticar.

A execução da ADAL e da MSAL na mesma aplicação de produção não é suportada. No entanto, se estiver apenas a testar e migrar os seus utilizadores da ADAL Objective-C para mSAL para iOS e macOS, pode continuar a utilizar o [ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). É a única versão que funciona com a MSAL na mesma aplicação. Não haverá novas atualizações de funcionalidades para esta versão ADAL, pelo que esta deve ser utilizada apenas para fins de migração e teste. A sua aplicação não deve contar com a coexistência a longo prazo da ADAL e da MSAL.

A coexistência da ADAL e da MSAL na mesma aplicação não é suportada.
A coexistência da ADAL e da MSAL entre várias aplicações é totalmente suportada.

## <a name="practical-migration-steps"></a>Passos práticos de migração

### <a name="app-registration-migration"></a>Migração de registo de aplicativos

Não precisa de alterar a sua aplicação AAD existente para mudar para MSAL e ativar contas AAD. No entanto, se a sua aplicação baseada no ADAL não apoiar a autenticação intermediada, terá de registar um novo URI redirecionado para a aplicação antes de poder mudar para MSAL.

O uri redirecionado deve estar neste formato: `msauth.<app.bundle.id>://auth`. Substitua `<app.bundle.id>` com o pacote de identificação da sua aplicação. Especifique o URI redirecionamento no [portal Azure](https://aka.ms/MobileAppReg).

Apenas para o iOS, para apoiar a autenticação baseada em CERT, é necessário registar um URI adicional na sua aplicação e no portal Azure no seguinte formato: `msauth://code/<broker-redirect-uri-in-url-encoded-form>`. Por exemplo, `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Recomendamos que todas as aplicações registem uris redirecionados.

Se desejar adicionar suporte para consentimento incremental, selecione as APIs e permissões a sua aplicação está configurada para solicitar acesso no registo da sua aplicação ao abrigo do separador de **permissões DaPI.**

Se está a migrar da ADAL e quer apoiar as contas AAD e MSA, o seu registo de candidatura seletiva tem de ser atualizado para suportar ambas. Não recomendamos que atualize a sua aplicação de produção existente para suportar imediatamente a AAD como a MSA. Em vez disso, crie outro ID do cliente que suporte tanto a AAD como a MSA para testes, e depois de verificar que todos os cenários funcionam, atualize a app existente.

### <a name="add-msal-to-your-app"></a>Adicione MSAL à sua aplicação

Pode adicionar MSAL SDK à sua aplicação utilizando a sua ferramenta de gestão de pacotes preferida. Consulte [as instruções detalhadas aqui](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Atualize o ficheiro Info.plist da sua aplicação

Apenas para iOS, adicione o sistema URI redirecionamento da sua aplicação ao seu ficheiro info.plist. Para aplicações compatíveis com corretor ADAL, já deve estar lá. O regime de redirecionamento padrão mSAL uri estará no formato: `msauth.<app.bundle.id>`.  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Adicione os seguintes esquemas à lista info.plist da sua aplicação em `LSApplicationQueriesSchemes`.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Atualize o seu código AppDelegate

Apenas para iOS, adicione o seguinte ao seu ficheiro AppDelegate.m:

Objetivo C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

Se estiver a utilizar o **Xcode 11,** deverá colocar a chamada MSAL no ficheiro `SceneDelegate`.
Se apoiar tanto o UISceneDelegate como o UIApplicationDelegate para a compatibilidade com iOS mais antigo, o backback do MSAL terá de ser colocado em ambos os ficheiros.

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

Swift:

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

Isto permite que a MSAL lide com as respostas do corretor e do componente web.
Isto não era necessário na ADAL, uma vez que "swizzed" métodos de delegado de aplicações automaticamente. Adicioná-lo manualmente é menos propenso a erros e dá à aplicação mais controlo.

### <a name="enable-token-caching"></a>Ativar o caching de token

Por padrão, a MSAL coloca as fichas da sua aplicação no porta-chaves iOS ou macOS. 

Para permitir o cache do token:
1. Certifique-se de que a sua candidatura está devidamente assinada
2. Vá ao seu separador De Projeto Xcode > **Capabilities > ** **ativar a partilha de porta-chaves**
3. Clique **+** e introduza uma seguinte entrada **de Grupos keychain:** 3.a Para iOS, introduza `com.microsoft.adalcache` 3.b Para que o macOS introduza `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Crie a MSALPublicClientApplication e mude para a sua aquisiçãoToken e adquira chamadas TokeSilent

Pode criar `MSALPublicClientApplication` utilizando o seguinte código:

Objetivo C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Em seguida, ligue para a API de gestão de conta para ver se há alguma conta no cache:

Objetivo C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

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

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Se for encontrada uma conta, ligue para a API `acquireTokenSilent` MSAL:

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

Swift:

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



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicação
