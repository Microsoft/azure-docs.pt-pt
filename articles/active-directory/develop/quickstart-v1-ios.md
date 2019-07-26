---
title: Compilar um aplicativo iOS integrado ao Azure AD para entrar usando o OAuth 2,0 | Microsoft Docs
description: Saiba como iniciar sessão dos utilizadores e chamar a Microsoft Graph API a partir de uma aplicação iOS.
services: active-directory
documentationcenter: ios
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: brandwe
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a82a7cad9b9176589824b6febb5cfdde89fce8a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380872"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Início rápido: Conectar usuários e chamar a API de Microsoft Graph de um aplicativo iOS

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

O Azure Active Directory (Azure AD) fornece a Biblioteca de Autenticação do Active Directory (ADAL) para clientes iOS que precisam de aceder a recursos protegidos. A ADAL simplifica o processo que a sua aplicação utiliza para obter os tokens de acesso. 

Neste início rápido, vai criar uma aplicação de Lista de Tarefas Objective C, que:

* Obtém tokens de acesso para chamar a Graph API do Azure AD através do protocolo de autenticação OAuth 2.0
* Procura num diretório utilizadores com um determinado alias

Para criar a aplicação funcional e completa, precisa de:

1. Registar a aplicação com o Azure AD.
1. Instalar e configurar a ADAL.
1. Utilizar a ADAL para obter os tokens do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, conclua estes pré-requisitos:

* [Transfira a estrutura da aplicação](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [transfira o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).
* Tenha um inquilino do Azure AD no qual possa criar utilizadores e registar uma aplicação. Se ainda não tiver um inquilino, [saiba como obter um](quickstart-create-new-tenant.md).

> [!TIP]
> Experimente o [portal do programador](https://identity.microsoft.com/Docs/iOS) para começar a trabalhar com o Azure AD em apenas alguns minutos. O portal do programador orienta-o ao longo do processo de registo de uma aplicação e de integração do Azure AD no seu código. Quando terminar, terá uma aplicação simples na qual poderá autenticar utilizadores no seu inquilino e um back-end que pode aceitar tokens e executar a validação.

## <a name="step-1-determine-what-your-redirect-uri-is-for-ios"></a>Passo 1: Determinar qual é o URI de redirecionamento para iOS

Para iniciar em segurança as suas aplicações em alguns cenários de SSO, terá de criar um *URI de redirecionamento* num formato específico. Um URI de redirecionamento serve para garantir que os tokens voltam à aplicação correta que os pediu.

O formato iOS para um URI de redirecionamento é:

```
<app-scheme>://<bundle-id>
```

* **app-scheme** - Está registado no projeto XCode e é a forma como outras aplicações podem chamar a sua. Pode encontrar o app-scheme em **Info.plist > URL types > URL Identifier**. Se ainda não tiver um ou mais configurados, crie um app-scheme.
* **bundle-id** - É o Identificador do Grupo que se encontra em **identity** nas definições do seu projeto XCode.

Um exemplo deste código de início rápido:

***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="step-2-register-the-directorysearcher-application"></a>Passo 2: Registrar o aplicativo DirectorySearcher

Para configurar a aplicação para obter tokens, terá de registar a aplicação no seu inquilino do Azure AD e conceder-lhe permissão para aceder à Graph API do Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra superior, selecione a sua conta. Na lista **Diretório**, escolha o inquilino do Active Directory onde pretende registar a aplicação.
3. Selecione **Todos os serviços** no painel de navegação mais à esquerda e, em seguida, selecione **Azure Active Directory**.
4. Selecione **registros de aplicativo**e, em seguida, selecione **novo registro**.
5. Siga os prompts para criar um novo aplicativo cliente.
    * **Nome** é o nome da aplicação e descreve a sua aplicação aos utilizadores finais.
    * **URI de Redirecionamento** é uma combinação de esquema e cadeia de caracteres que o Azure AD utiliza para devolver respostas de tokens. Introduza um valor que seja específico da sua aplicação e que se baseie nas informações do URI de redirecionamento anterior. Selecione também **cliente público (móvel e área de trabalho)** na lista suspensa.
6. Depois de concluir o registo, o Azure AD atribui um ID exclusivo à sua aplicação. Este valor vai ser preciso nas secções seguintes, por isso, copie-o a partir do separador da aplicação.
7. Na página **permissões de API** , selecione **Adicionar uma permissão**. Dentro **de selecionar uma API** , selecione ***Microsoft Graph***.
8. Em **permissões delegadas**, selecione a permissão **usuário. ler**e, em seguida, clique em **Adicionar** para salvar. Esta permissão configura a sua aplicação para consultar utilizadores na Graph API do Azure AD.

## <a name="step-3-install-and-configure-adal"></a>Passo 3: Instalar e configurar a ADAL

Agora que já tem uma aplicação no Azure AD, pode instalar a ADAL e escrever o seu código relacionado com identidade. Para que a ADAL comunique com o Azure AD, terá de fornecer algumas informações sobre o registo da sua aplicação.

1. Comece por adicionar a ADAL ao projeto DirectorySearcher utilizando o CocoaPods.

    ```
    $ vi Podfile
    ```
1. Adicione o seguinte a este podfile:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADAL'
    ```

1. Carregue o podfile utilizando o CocoaPods. Este passo cria uma nova área de trabalho XCode que irá carregar.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

1. No projeto QuickStart, abra o ficheiro plist `settings.plist`.
1. Substitua os valores dos elementos na secção para utilizar os mesmos valores que introduziu no portal do Azure. O seu código faz referência a estes valores sempre que utilizar a ADAL.
    * `tenant` é o domínio do inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com.
    * `clientId` é o ID de cliente da sua aplicação que copiou do portal.
    * `redirectUri` é o URL de redirecionamento que registou no portal.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Passo 4: Usar a ADAL para obter tokens do Azure AD

O princípio básico subjacente à ADAL é o de que, sempre que a sua aplicação precisar de um token de acesso, basta chamar um completionBlock `+(void) getToken :` e a ADAL trata do resto.

1. No projeto `QuickStart`, abra `GraphAPICaller.m` e localize o comentário `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` perto do início.

    É aqui que vai passar à ADAL as coordenadas através de um CompletionBlock, para comunicar com o Azure AD, e indicar como deve colocar os tokens em cache.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. Tem de utilizar este token para procurar utilizadores no grafo. Localize o comentário `// TODO: implement SearchUsersList`. Este método faz um pedido GET à Graph API do Azure AD para consultar utilizadores cujo UPN comece pelo termo de pesquisa especificado. 

    Para consultar a Graph API do Azure AD, precisa de incluir um access_token no cabeçalho `Authorization` do pedido. É aqui que a ADAL entra.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```

3. Quando a aplicação pedir um token ao chamar `getToken(...)`, a ADAL tenta devolver um token sem pedir ao utilizador as credenciais. Se a ADAL determinar que o utilizador tem de iniciar sessão para obter um token, irá apresentar uma caixa de diálogo de início de sessão, recolher as credenciais do utilizador e, em seguida, devolver um token após a autenticação com êxito. Se, por qualquer motivo, a ADAL não conseguir devolver um token, lança uma `AdalException`.

> [!NOTE]
> O objeto `AuthenticationResult` contém um objeto `tokenCacheStoreItem` que pode utilizar para recolher as informações de que a sua aplicação precisa. No QuickStart, `tokenCacheStoreItem` é utilizado para determinar se a autenticação já foi feita.

## <a name="step-5-build-and-run-the-application"></a>Passo 5: Compilar e executar a aplicação

Parabéns! Tem agora uma aplicação iOS funcional que pode autenticar utilizadores, chamar em segurança APIs Web através da OAuth 2.0 e obter informações básicas sobre o utilizador.

Caso ainda não o tenha feito, está na altura de preencher o seu inquilino com alguns utilizadores.

1. Inicie a aplicação QuickStart e, em seguida, inicie sessão com um desses utilizadores.
1. Procure outros utilizadores com base no respetivo UPN.
1. Feche a aplicação e, em seguida, volte a iniciá-la. Repare que a sessão do utilizador permanece intacta.

A ADAL facilita a incorporação de todas estas funcionalidades de identidade comum na sua aplicação. Faz todo o trabalho desagradável por si, como a gestão da cache, suporte do protocolo OAuth, apresentação ao utilizador de uma IU para início de sessão e atualização de tokens expirados. Tudo o que precisa realmente de fazer é uma única chamada à API, `getToken`.

Para sua referência, o exemplo concluído (sem os valores de configuração) é disponibilizado no [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).

## <a name="next-steps"></a>Passos Seguintes

Agora, pode avançar para cenários adicionais. Sugerimos que experimente estes em seguida:

* [Proteger uma API Web Node.JS com o Azure AD](quickstart-v1-nodejs-webapi.md)
* Saiba [como ativar o SSO entre várias aplicações em iOS com a ADAL](howto-v1-enable-sso-ios.md)  
