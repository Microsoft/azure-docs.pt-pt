---
title: Usando o AppAuth em um aplicativo iOS no Azure Active Directory B2C | Microsoft Docs
description: Este artigo mostra como criar um aplicativo iOS que usa o AppAuth com o Azure Active Directory B2C para gerenciar identidades de usuário e autenticar usuários.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0fb5341c2e7ee55391cb38251b0ea66b55b93301
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469150"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: entrar usando um aplicativo iOS

A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. O uso de um protocolo padrão aberto oferece mais opções de desenvolvedor ao selecionar uma biblioteca para integração com nossos serviços. Fornecemos este passo a passos e outros como ele para ajudar os desenvolvedores a escrever aplicativos que se conectam à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação especificação rfc6749 OAuth2](https://tools.ietf.org/html/rfc6749) é capaz de se conectar à plataforma de identidade da Microsoft.

> [!WARNING]
> A Microsoft não fornece correções para bibliotecas de terceiros e não fez uma análise dessas bibliotecas. Este exemplo está usando uma biblioteca de terceiros chamada AppAuth que foi testada quanto à compatibilidade em cenários básicos com o Azure AD B2C. Problemas e solicitações de recursos devem ser direcionados para o projeto de código-fonte aberto da biblioteca. Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Se você é novo no OAuth2 ou no OpenID Connect, grande parte dessa configuração de exemplo pode não fazer muito sentido para você. Recomendamos que leia a breve [descrição geral do protocolo aqui documentado](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C
Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contêiner para todos os usuários, aplicativos, grupos e muito mais. Se ainda não tiver um, [crie um diretório do B2C](tutorial-create-tenant.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, registre um aplicativo em seu locatário de Azure AD B2C. Isso dá ao Azure AD as informações de que ele precisa para se comunicar de forma segura com seu aplicativo.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Registre também seu URI de redirecionamento personalizado para uso em uma etapa posterior. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Criar seus fluxos de usuário
No Azure AD B2C, cada experiência de usuário é definida por um [fluxo de usuário](active-directory-b2c-reference-policies.md). Este aplicativo contém uma experiência de identidade: uma entrada e uma inscrição combinadas. Ao criar o fluxo de usuário, certifique-se de:

* Em **atributos de inscrição**, selecione o nome de **exibição**do atributo.  Você também pode selecionar outros atributos.
* Em **declarações do aplicativo**, selecione o **nome de exibição** de declarações e a **ID de objeto do usuário**. Você também pode selecionar outras declarações.
* Copie o **nome** de cada fluxo de usuário depois de criá-lo. O nome do fluxo de usuário é prefixado com `b2c_1_` quando você salva o fluxo do usuário.  Você precisará do nome do fluxo do usuário mais tarde.

Depois de criar seus fluxos de usuário, você estará pronto para compilar seu aplicativo.

## <a name="download-the-sample-code"></a>Baixar o código de exemplo
Fornecemos um exemplo funcional que usa AppAuth com Azure AD B2C [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Você pode baixar o código e executá-lo. Para usar seu próprio locatário Azure AD B2C, siga as instruções em [README.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Este exemplo foi criado seguindo as instruções do LEIAme do [projeto APPAUTH do Ios no GitHub](https://github.com/openid/AppAuth-iOS). Para obter mais detalhes sobre como o exemplo e a biblioteca funcionam, consulte o LEIAme do AppAuth no GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificando seu aplicativo para usar o Azure AD B2C com AppAuth

> [!NOTE]
> O AppAuth dá suporte ao iOS 7 e superior.  No entanto, para dar suporte a logons sociais no Google, o SFSafariViewController é necessário, o que exige o iOS 9 ou superior.
>

### <a name="configuration"></a>Configuração

Você pode configurar a comunicação com Azure AD B2C especificando o ponto de extremidade de autorização e os URIs de ponto de extremidade do token.  Para gerar esses URIs, você precisará das seguintes informações:
* ID do locatário (por exemplo, contoso.onmicrosoft.com)
* Nome do fluxo do usuário (por exemplo, B2C\_1\_inscrição)

O URI do ponto de extremidade do token pode ser gerado substituindo a ID de\_do locatário e o nome da\_de política na seguinte URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

O URI do ponto de extremidade de autorização pode ser gerado substituindo a ID de\_do locatário e o nome da\_de política na seguinte URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Execute o seguinte código para criar o objeto AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorizando

Depois de configurar ou recuperar uma configuração de serviço de autorização, uma solicitação de autorização pode ser construída. Para criar a solicitação, você precisará das seguintes informações:

* ID do cliente (ID do aplicativo) que você registrou anteriormente. Por exemplo, `00000000-0000-0000-0000-000000000000`.
* URI de redirecionamento personalizado que você registrou anteriormente. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Ambos os itens devem ter sido salvos quando você estava [registrando seu aplicativo](#create-an-application).

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

Para configurar seu aplicativo para manipular o redirecionamento para o URI com o esquema personalizado, você precisa atualizar a lista de ' esquemas de URL ' no seu info. pList:
* Abra o info. pList.
* Passe o mouse sobre uma linha como "empacotar código de tipo de so" e clique no símbolo de \+.
* Renomeie a nova linha ' tipos de URL '.
* Clique na seta à esquerda de ' tipos de URL ' para abrir a árvore.
* Clique na seta à esquerda de ' item 0 ' para abrir a árvore.
* Renomeie o primeiro item abaixo do item 0 para ' esquemas de URL '.
* Clique na seta à esquerda de ' esquemas de URL ' para abrir a árvore.
* Na coluna ' valor ', há um campo em branco à esquerda do ' item 0 ' sob ' esquemas de URL '.  Defina o valor para o esquema exclusivo do seu aplicativo.  O valor deve corresponder ao esquema usado em redirectURL ao criar o objeto OIDAuthorizationRequest.  No exemplo, o esquema ' com. onmicrosoft. fabrikamb2c. ExampleApp ' é usado.

Consulte o [Guia do APPAUTH](https://openid.github.io/AppAuth-iOS/) sobre como concluir o restante do processo. Se você precisar começar rapidamente com um aplicativo em funcionamento, confira [o exemplo](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Siga as etapas em [README.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) para inserir sua própria configuração de Azure ad B2C.

Estamos sempre abertos para comentários e sugestões! Se você tiver dificuldades com este artigo ou tiver recomendações para melhorar esse conteúdo, agradecemos seus comentários na parte inferior da página. Para solicitações de recursos, adicione-as ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
