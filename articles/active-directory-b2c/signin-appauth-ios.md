---
title: Use appAuth numa aplicação iOS
titleSuffix: Azure AD B2C
description: Como criar uma aplicação iOS que utiliza o AppAuth com o Azure Ative Directory B2C para gerir as identidades dos utilizadores e autenticar os utilizadores.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c659280ebc8c91b53cbc3a176c84397edd942c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186833"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Iniciar sessão utilizando uma aplicação iOS

A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. A utilização de um protocolo padrão aberto oferece mais escolha de desenvolvedor ao selecionar uma biblioteca para integrar com os nossos serviços. Nós fornecemos este walkthrough e outros como ele para ajudar desenvolvedores com aplicações de escrita que se conectam à plataforma Microsoft Identity. A maioria das bibliotecas que implementam [a especificação RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) são capazes de se ligar à plataforma Microsoft Identity.

> [!WARNING]
> A Microsoft não fornece correções para bibliotecas de terceiros e não fez uma revisão dessas bibliotecas. Esta amostra está a usar uma biblioteca de terceiros chamada AppAuth que foi testada para compatibilidade em cenários básicos com o Azure AD B2C. As questões e pedidos de funcionalidades devem ser direcionados para o projeto de código aberto da biblioteca. Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Se é novo no OAuth2 ou OpenID Connect, grande parte desta configuração da amostra pode não fazer muito sentido para si. Recomendamos que leia a breve [descrição geral do protocolo aqui documentado](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C
Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um recipiente para todos os seus utilizadores, apps, grupos e muito mais. Se ainda não tiver um, [crie um diretório do B2C](tutorial-create-tenant.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, registe uma inscrição no seu inquilino Azure AD B2C. Isto dá ao Azure AD a informação de que necessita para comunicar de forma segura com a sua aplicação.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Grave o ID de **Aplicação (cliente)** para utilização num passo posterior.

Também grave o seu URI personalizado para utilização num passo posterior. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Crie os fluxos de utilizador
No Azure AD B2C, cada experiência do utilizador é definida por um [fluxo de utilizador](user-flow-overview.md). Esta aplicação contém uma experiência de identidade: um início combinado e inscrição. Quando criar o fluxo do utilizador, certifique-se de:

* Sob **atributos de inscrição,** selecione o **nome**do display do atributo .  Também pode selecionar outros atributos.
* Em **alegações de aplicação,** selecione o nome do **display** de reclamações e o ID do objeto **do utilizador**. Também pode selecionar outras reclamações.
* Copie o **nome** de cada fluxo de cada utilizador depois de o criar. O nome de fluxo `b2c_1_` do utilizador está pré-fixado quando guarda o fluxo do utilizador.  Precisa do nome de fluxo do utilizador mais tarde.

Depois de ter criado os fluxos de utilizador, está pronto para construir a sua aplicação.

## <a name="download-the-sample-code"></a>Descarregue o código da amostra
Fornecemos uma amostra de trabalho que utiliza appAuth com Azure AD B2C [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Pode descarregar o código e executá-lo. Para utilizar o seu próprio inquilino Azure AD B2C, siga as instruções no [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Esta amostra foi criada seguindo as instruções da README pelo [projeto iOS AppAuth no GitHub.](https://github.com/openid/AppAuth-iOS) Para mais detalhes sobre como a amostra e a biblioteca funcionam, consulte a AppAuth README no GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificar a sua aplicação para utilizar o Azure AD B2C com appAuth

> [!NOTE]
> AppAuth suporta o iOS 7 ou superior.  No entanto, para suportar logins sociais no Google, o SFSafariViewController é necessário que exija iOS 9 ou superior.
>

### <a name="configuration"></a>Configuração

Pode configurar a comunicação com o Azure AD B2C especificando tanto o ponto final de autorização como os URIs finais simbólicos.  Para gerar estes URIs, precisa das seguintes informações:
* ID do inquilino (por exemplo, contoso.onmicrosoft.com)
* Nome de fluxo do utilizador\_(por exemplo, B2C 1\_SignUpIn)

O ponto final simbólico URI pode ser gerado\_substituindo\_o ID do Arrendatário e o Nome da Apólice no seguinte URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

O ponto final de autorização URI pode\_ser gerado\_substituindo o ID do Arrendatário e o Nome de Política no seguinte URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Executar o seguinte código para criar o seu objeto de Configuração de Serviçode Autorização:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorizar

Depois de configurar ou recuperar uma configuração de serviço de autorização, pode ser construído um pedido de autorização. Para criar o pedido, necessita das seguintes informações:

* ID do cliente (ID de aplicação) que gravou anteriormente. Por exemplo, `00000000-0000-0000-0000-000000000000`.
* Redirecione o URI personalizado que gravou anteriormente. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Ambos os itens deveriam ter sido guardados quando estava [a registar a sua aplicação](#create-an-application).

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Para configurar a sua aplicação para lidar com o redirecionamento para o URI com o esquema personalizado, precisa de atualizar a lista de 'Esquemas DE URL' no seu Info.pList:
* Open Info.pList.
* Paire sobre uma linha como 'Bundle \+ OS Type Code' e clique no símbolo.
* Mude o nome da nova linha 'TIPOS DE URL'.
* Clique na seta à esquerda dos 'tipos de URL' para abrir a árvore.
* Clique na seta à esquerda do 'Item 0' para abrir a árvore.
* Mude o nome do primeiro item por baixo do item 0 para "URL Schemes".
* Clique na seta à esquerda dos "Esquemas de URL" para abrir a árvore.
* Na coluna 'Valor', existe um campo em branco à esquerda do 'Item 0' por baixo dos "Esquemas de URL".  Detete o valor do esquema único da sua aplicação.  O valor deve corresponder ao esquema utilizado no redirecionamento url quando criar o objeto OIDAuthorizationRequest.  Na amostra, é utilizado o esquema 'com.onmicrosoft.fabrikamb2c.exampleapp'.

Consulte o [guia AppAuth](https://openid.github.io/AppAuth-iOS/) sobre como completar o resto do processo. Se precisar de começar rapidamente com uma aplicação de trabalho, confira [a amostra](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Siga os passos do [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) para introduzir a sua própria configuração Azure AD AD B2C.

Estamos sempre abertos a feedback e sugestões! Se tiver alguma dificuldade com este artigo, ou tiver recomendações para melhorar este conteúdo, gostaríamos do seu feedback na parte inferior da página. Para pedidos de funcionalidade, adicione-os ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
