---
title: Configure fornecedores de identidade (MSAL iOS/macOS) [ Azure
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
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085205"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Como: Configure mSAL para iOS e macOS para utilizar diferentes fornecedores de identidade

Este artigo irá mostrar-lhe como configurar a sua aplicação da biblioteca de autenticação Microsoft para iOS e macOS (MSAL) para diferentes autoridades como o Azure Ative Directory (Azure AD), Business-to-Consumer (B2C), nuvens soberanas e utilizadores convidados.  Ao longo deste artigo, pode geralmente pensar numa autoridade como um fornecedor de identidade.

## <a name="default-authority-configuration"></a>Configuração da autoridade padrão

`MSALPublicClientApplication`é configurado com um URL `https://login.microsoftonline.com/common`de autoridade padrão de , que é adequado para a maioria dos cenários de Diretório Ativo Azure (AAD). A menos que esteja implementando cenários avançados como nuvens nacionais, ou trabalhando com B2C, não precisará mudá-lo.

> [!NOTE]
> A autenticação moderna com os Serviços da Federação de Directórioativo Ativo como fornecedor de identidade (ADFS) não é suportada (ver [ADFS para Desenvolvedores](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) para obter detalhes). A ADFS é apoiada através da federação.

## <a name="change-the-default-authority"></a>Alterar a autoridade por defeito

Em alguns cenários, como o negócio-para-consumidor (B2C), poderá ter de alterar a autoridade por defeito.

### <a name="b2c"></a>B2C

Para trabalhar com o B2C, a [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) requer uma configuração de autoridade diferente. A MSAL reconhece um formato URL de uma autoridade como B2C por si só. O formato de autoridade `https://<host>/tfp/<tenant>/<policy>`B2C `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`reconhecido é, por exemplo. No entanto, também pode utilizar qualquer outra urls de autoridade B2C suportada declarando autoridade b2C explicitamente.

Para suportar um formato de URL `MSALB2CAuthority` arbitrário para B2C, pode ser definido com um URL arbitrário, como este:

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

Adicione cada autoridade B2C diferente à lista de autoridades conhecidas, mesmo que as autoridades só diferam na política.

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

Quando a sua aplicação solicita uma nova política, o URL da autoridade precisa de ser alterado porque o URL da autoridade é diferente para cada política. 

Para configurar uma aplicação `@property MSALAuthority *authority` B2C, `MSALB2CAuthority` `MSALPublicClientApplicationConfig` definir `MSALPublicClientApplication`com uma instância de entrada antes de criar, como esta:

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

### <a name="sovereign-clouds"></a>Nuvens soberanas

Se a sua aplicação estiver numa nuvem soberana, poderá `MSALPublicClientApplication`ter de alterar o URL de autoridade no . O exemplo que se segue define o URL da autoridade para trabalhar com a nuvem aAD alemã:

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

Pode ter de passar diferentes miras a cada nuvem soberana. Que âmbitos para enviar depende do recurso que está a usar. Por exemplo, você `"https://graph.microsoft.com/user.read"` pode usar `"https://graph.microsoft.de/user.read"` em nuvem mundial, e em nuvem alemã.

### <a name="signing-a-user-into-a-specific-tenant"></a>Assinar um utilizador em um inquilino específico

Quando o URL da `"login.microsoftonline.com/common"`autoridade estiver definido para , o utilizador será assinado no seu inquilino de casa. No entanto, algumas aplicações podem precisar de contratar o utilizador para um inquilino diferente e algumas aplicações só funcionam com um único inquilino.

Para assinar o utilizador num inquilino `MSALPublicClientApplication` específico, configure com uma autoridade específica. Por exemplo:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

O seguinte mostra como assinar um utilizador num inquilino específico:

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

A `MSALAuthority` classe é a classe abstrata base para as aulas de autoridade mSAL. Não tente criar instâncias que `alloc` `new`utilizem ou . Em vez disso, ou cria uma`MSALAADAuthority` `MSALB2CAuthority`das suas subclasses `authorityWithURL:error:` diretamente ( ou utilize o método de fábrica para criar subclasses utilizando um URL de autoridade.

Use `url` a propriedade para obter um URL de autoridade normalizado. Parâmetros extras e componentes ou fragmentos de caminho que não façam parte da autoridade não estarão no URL de autoridade normalizado devolvido.

Seguem-se subclasses que `MSALAuthority` pode instantaneamente, dependendo da autoridade que pretende utilizar.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`representa uma autoridade aAD. O url da autoridade deve estar `<port>` no seguinte formato, onde é opcional:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority`representa uma autoridade B2C. Por predefinição, o url da autoridade B2C `<port>` deve `https://<host>:<port>/tfp/<tenant>/<policy>`estar no seguinte formato, onde é opcional: . No entanto, a MSAL também apoia outros formatos arbitrários da autoridade B2C.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicação
