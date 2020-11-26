---
title: Configure fornecedores de identidade (MSAL iOS/macOS) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como utilizar diferentes autoridades como B2C, nuvens soberanas e utilizadores convidados, com MSAL para iOS e macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d8a176fff0da932d0fafd40b9ab895b635acc5f6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169448"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Como: Configurar a MSAL para iOS e macOS utilizarem diferentes fornecedores de identidade

Este artigo irá mostrar-lhe como configurar a sua app de biblioteca de autenticação microsoft para iOS e macOS (MSAL) para diferentes autoridades como O Azure Ative Directory (Azure AD), Business-to-Consumer (B2C), nuvens soberanas e utilizadores convidados.  Ao longo deste artigo, pode geralmente pensar numa autoridade como um fornecedor de identidade.

## <a name="default-authority-configuration"></a>Configuração de autoridade predefinido

`MSALPublicClientApplication` é configurado com um URL de autoridade padrão de , que é adequado para a maioria dos `https://login.microsoftonline.com/common` cenários do Azure Ative Directory (AAD). A menos que esteja a implementar cenários avançados como nuvens nacionais, ou a trabalhar com b2C, não precisará mudá-lo.

> [!NOTE]
> A autenticação moderna com serviços da Federação de Diretório Ativo como fornecedor de identidade (ADFS) não é suportada (ver [ADFS para desenvolvedores](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) para mais detalhes). A ADFS é apoiada através da federação.

## <a name="change-the-default-authority"></a>Alterar a autoridade padrão

Em alguns cenários, como negócio-a-consumidor (B2C), poderá ter de alterar a autoridade por defeito.

### <a name="b2c"></a>B2C

Para trabalhar com o B2C, a [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) requer uma configuração de autoridade diferente. A MSAL reconhece um formato URL de autoridade como B2C por si só. O reconhecido formato de autoridade B2C `https://<host>/tfp/<tenant>/<policy>` é, por `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` exemplo. No entanto, também pode utilizar quaisquer outros URLs de autoridade B2C apoiados declarando a autoridade como autoridade B2C explicitamente.

Para suportar um formato url arbitrário para B2C, `MSALB2CAuthority` pode ser definido com um URL arbitrário, como este:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Todas as autoridades B2C que não utilizem o formato de autoridade B2C padrão devem ser declaradas como autoridades conhecidas.

Adicione cada autoridade B2C diferente à lista de autoridades conhecidas, mesmo que as autoridades apenas diferam na política.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Quando a sua aplicação solicita uma nova política, o URL da autoridade precisa de ser alterado porque o URL de autoridade é diferente para cada política. 

Para configurar uma aplicação B2C, definida `@property MSALAuthority *authority` com um exemplo de in antes de `MSALB2CAuthority` `MSALPublicClientApplicationConfig` `MSALPublicClientApplication` criar, como esta:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Clouds soberanas

Se a sua aplicação for executado numa nuvem soberana, poderá ter de alterar o URL de autoridade no `MSALPublicClientApplication` . O exemplo a seguir define o URL da autoridade para trabalhar com a nuvem alemã AAD:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Pode ser necessário passar diferentes âmbitos a cada nuvem soberana. Quais os âmbitos a enviar dependem do recurso que está a usar. Por exemplo, você pode usar `"https://graph.microsoft.com/user.read"` em nuvem mundial, e `"https://graph.microsoft.de/user.read"` em nuvem alemã.

### <a name="signing-a-user-into-a-specific-tenant"></a>Inscrever um utilizador num inquilino específico

Quando o URL da autoridade estiver definido `"login.microsoftonline.com/common"` para, o utilizador será assinado no seu inquilino de casa. No entanto, algumas aplicações podem precisar de inscrever o utilizador num inquilino diferente e algumas aplicações apenas funcionam com um único inquilino.

Para inscrever o utilizador num inquilino específico, configuure-se `MSALPublicClientApplication` com uma autoridade específica. Por exemplo:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

O seguinte mostra como inscrever um utilizador num inquilino específico:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Autoridades apoiadas

### <a name="msalauthority"></a>MSALAuthority

A `MSALAuthority` classe é a classe abstrata base para as aulas de autoridade msal. Não tente criar exemplos de utilização `alloc` ou `new` . Em vez disso, ou cria uma das suas subclasses diretamente `MSALAADAuthority` (, `MSALB2CAuthority` ) ou utiliza o método de fábrica para criar `authorityWithURL:error:` subclasses usando um URL de autoridade.

Use a `url` propriedade para obter um URL de autoridade normalizado. Parâmetros extras e componentes do caminho ou fragmentos que não fazem parte da autoridade não estarão na URL de autoridade normalizada devolvida.

Seguem-se subclasses `MSALAuthority` de que pode instantaneamente, dependendo da autoridade que pretende utilizar.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` representa uma autoridade da AAD. O url de autoridade deve estar no seguinte formato, sempre que `<port>` seja opcional: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` representa uma autoridade B2C. Por predefinição, o url de autoridade B2C deve estar no seguinte formato, onde `<port>` é opcional: `https://<host>:<port>/tfp/<tenant>/<policy>` . No entanto, a MSAL também suporta outros formatos arbitrários de autoridade B2C.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)