---
title: SSO entre apps ADAL & MSAL (iOS/macOS) - Plataforma de identidade da Microsoft Azure
description: ''
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 7a8a1667ba1ca2a99c053c6941e3ba778299fd53
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880755"
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

A MSAL e a ADAL utilizam diferentes identificadores de conta. A ADAL usa a UPN como o seu principal identificador de conta. A MSAL utiliza um identificador de conta não apresentável que se baseia num ID `sub` de objeto e num ID de inquilino para contas AAD, e uma reclamação para outros tipos de contas.

Quando recebe `MSALAccount` um objeto no resultado da MSAL, contém `identifier` um identificador de conta na propriedade. O pedido deve utilizar este identificador para posteriores pedidos silenciosos.

Além `identifier`disso, `MSALAccount` o objeto contém `username`um identificador exibivel chamado . Isso traduz-se `userId` em ADAL. `username`não é considerado um identificador único e pode mudar a qualquer momento, pelo que deve ser usado apenas para cenários de compatibilidade retrógrada com a ADAL. A MSAL suporta consultas `username` de `identifier`cache utilizando ou, sempre que `identifier` é recomendada a consulta por.

O quadro seguinte resume as diferenças entre a ADAL e a MSAL:

| Identificador de conta                | MSAL                                                         | ADAL 2.7.x      | ADAL mais antigo (antes de ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| identificador exibivel            | `username`                                                   | `userId`        | `userId`                       |
| identificador único não apresentável | `identifier`                                                 | `homeAccountId` | N/D                            |
| Nenhuma identidade de conta conhecida               | Consulta de todas `allAccounts:` as contas através da API em`MSALPublicClientApplication` | N/D             | N/D                            |

Esta é `MSALAccount` a interface que fornece os identificadores:

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

Se tiver uma aplicação MSAL e uma aplicação ADAL, e o utilizador assinar pela primeira vez na aplicação `username` baseada `MSALAccount` em MSAL, pode obter SSO na aplicação ADAL, guardando o objeto e passando-o para a sua aplicação baseada no ADAL como `userId`. A ADAL pode então encontrar `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` a informação da conta silenciosamente com a API.

### <a name="sso-from-adal-to-msal"></a>SSO da ADAL para MSAL

Se tiver uma aplicação MSAL e uma aplicação ADAL, e o utilizador assinar pela primeira vez na aplicação baseada em ADAL, pode utilizar identificadores de utilizador ADAL para pesquisade conta no MSAL. Isto aplica-se também quando se migra da ADAL para a MSAL.

#### <a name="adals-homeaccountid"></a>HomeAccountId da ADAL

ADAL 2.7.x `homeAccountId` devolve `ADUserInformation` o objeto no resultado através desta propriedade:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`no ADAL's é `identifier` equivalente a mSAL. Pode guardar este identificador para utilizar no MSAL `accountForIdentifier:error:` para a procura de conta com a API.

#### <a name="adals-userid"></a>ADAL's`userId`

Se `homeAccountId` não estiver disponível, ou se tiver apenas o identificador `userId` visualizado, pode utilizar o ADAL's para pesquisar a conta no MSAL.

Na MSAL, primeiro procure `username` uma `identifier`conta por ou . Use `identifier` sempre para consulta se o tiver, `username` e use apenas como recuo. Se a conta for encontrada, `acquireTokenSilent` use a conta nas chamadas.

Objetivo C:

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

Swift:

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

Como `homeAccountId` não está disponível em versões ADAL mais antigas, `username`você precisa procurar contas usando o:

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

Objetivo C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

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

Objetivo C:

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

Swift:

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

Saiba mais sobre [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicação
