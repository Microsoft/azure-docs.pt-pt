---
title: SSO entre o ADAL & aplicativos MSAL (iOS/macOS) – plataforma de identidade da Microsoft | Azure
description: ''
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: be608019aa6a393891d9586005e5ef9c970a8bd1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712370"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Como: SSO entre aplicações ADAL e MSAL no macOS e iOS

A Microsoft Authentication Library (MSAL) para iOS pode partilhar o estado SSO com o [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) entre aplicações. Pode migrar as suas aplicações para o MSAL ao seu próprio ritmo, garantindo que os seus utilizadores continuarão a beneficiar de sso cross-app -- mesmo com uma mistura de aplicações baseadas em ADAL e MSAL.

Se procura orientação para configurar o SSO entre aplicações que utilizem o MSAL SDK, consulte [Silent SSO entre várias aplicações](single-sign-on-macos-ios.md#silent-sso-between-apps). Este artigo centra-se no SSO entre a ADAL e a MSAL.

As especificidades que implementam o SSO dependem da versão ADAL que está a usar.

## <a name="adal-27x"></a>ADAL 2.7.x

Esta secção cobre diferenças SSO entre MSAL e ADAL 2.7.x

### <a name="cache-format"></a>Formato cache

ADAL 2.7.x pode ler o formato de cache MSAL. Não precisa de fazer nada de especial para o SSO cross-app com a versão ADAL 2.7.x. No entanto, é preciso estar atento às diferenças de identificação de contas que essas duas bibliotecas apoiam.

### <a name="account-identifier-differences"></a>Diferenças de identificador de conta

A MSAL e a ADAL utilizam diferentes identificadores de conta. A ADAL usa a UPN como o seu principal identificador de conta. A MSAL utiliza um identificador de conta não apresentável que se baseia num ID de objeto e num ID de inquilino para contas AAD, e um pedido de `sub` para outros tipos de contas.

Quando recebe um objeto `MSALAccount` no resultado do MSAL, contém um identificador de conta na propriedade `identifier`. O pedido deve utilizar este identificador para posteriores pedidos silenciosos.

Além de `identifier`, `MSALAccount` objeto contém um identificador exibivel chamado `username`. Isso traduz-se em `userId` na ADAL. `username` não é considerado um identificador único e pode mudar a qualquer momento, pelo que deve ser usado apenas para cenários de compatibilidade retrógrada com a ADAL. A MSAL suporta consultas de cache utilizando `username` ou `identifier`, onde é recomendada a consulta por `identifier`.

O quadro seguinte resume as diferenças entre a ADAL e a MSAL:

| Identificador de conta                | MSAL                                                         | ADAL 2.7.x      | ADAL mais antigo (antes de ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| identificador exibivel            | `username`                                                   | `userId`        | `userId`                       |
| identificador único não apresentável | `identifier`                                                 | `homeAccountId` | N/A                            |
| Nenhuma identidade de conta conhecida               | Consulta de todas as contas através `allAccounts:` API em `MSALPublicClientApplication` | N/A             | N/A                            |

Esta é a interface `MSALAccount` que fornece os identificadores:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO da MSAL para a ADAL

Se tiver uma aplicação MSAL e uma aplicação ADAL, e o utilizador assinar pela primeira vez na aplicação baseada em MSAL, pode obter SSO na aplicação ADAL, guardando o `username` do objeto `MSALAccount` e passando-o para a sua aplicação baseada em ADAL como `userId`. A ADAL pode então encontrar a informação da conta silenciosamente com a API `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:`.

### <a name="sso-from-adal-to-msal"></a>SSO da ADAL para MSAL

Se tiver uma aplicação MSAL e uma aplicação ADAL, e o utilizador assinar pela primeira vez na aplicação baseada em ADAL, pode utilizar identificadores de utilizador ADAL para pesquisade conta no MSAL. Isto aplica-se também quando se migra da ADAL para a MSAL.

#### <a name="adals-homeaccountid"></a>HomeAccountId da ADAL

A ADAL 2.7.x devolve o `homeAccountId` no objeto `ADUserInformation` no resultado através desta propriedade:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` na ADAL's equivale a `identifier` na MSAL. Pode guardar este identificador para utilizar no MSAL para a procura de contas com a API `accountForIdentifier:error:`.

#### <a name="adals-userid"></a>O `userId` da ADAL

Se `homeAccountId` não estiver disponível, ou se tiver apenas o identificador visualizado, pode utilizar o `userId` da ADAL para pesquisar a conta no MSAL.

Na MSAL, primeiro procure uma conta em `username` ou `identifier`. Use sempre `identifier` para consulta se o tiver, e use apenas `username` como recuo. Se a conta for encontrada, utilize a conta no `acquireTokenSilent` chamadas.

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



ApIs de procura de conta apoiada pela MSAL:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

Esta secção cobre as diferenças de SSO entre MSAL e ADAL 2.x-2.6.

As versões ADAL mais antigas não suportam de forma nativa o formato de cache MSAL. No entanto, para garantir uma migração suave da ADAL para a MSAL, a MSAL pode ler o formato de cache ADAL mais antigo sem solicitar novamente as credenciais dos utilizadores.

Como `homeAccountId` não está disponível em versões ADAL mais antigas, você precisa procurar contas usando o `username`:

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Por exemplo:

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Em alternativa, pode ler todas as contas, que também lerão informações de conta da ADAL:

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
