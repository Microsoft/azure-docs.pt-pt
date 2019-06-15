---
title: Como utilizar SDK do iOS do Mobile Apps do Azure
description: Como utilizar SDK do iOS do Mobile Apps do Azure
services: app-service\mobile
documentationcenter: ios
author: conceptdev
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: b6f93cc3c35ab18ecd50ccd6b3090985497baabf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122460"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Como uso iOS biblioteca de cliente para aplicações móveis do Azure

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina-lhe executar tarefas comuns com a versão mais recente [SDK iOS do Mobile Apps do Azure][1]. Se estiver familiarizado com aplicações móveis do Azure, primeiro conclua [Início rápido de aplicações móveis do Azure] para criar um back-end, criar uma tabela e transfira um projeto do Xcode iOS criados previamente. Neste guia, vamos nos concentrar no SDK do iOS do lado do cliente. Para saber mais sobre o SDK do lado do servidor para o back-end, consulte o HOWTOs de SDK do servidor.

## <a name="reference-documentation"></a>Documentação de referência

A documentação de referência para o cliente do iOS SDK está localizado aqui: [IOS de aplicações móveis do Azure a referência de cliente][2].

## <a name="supported-platforms"></a>Plataformas suportadas

O SDK do iOS suporta projetos de Objective-C, projetos de Swift 2.2 e Swift 2.3 projetos para o iOS 8.0 ou posteriores de versões.

A autenticação de "servidor-fluxo" utiliza uma WebView para a interface do Usuário apresentada.  Se o dispositivo não é capaz de apresentar uma interface de Usuário WebView, em seguida, é necessário o outro método de autenticação que está fora do âmbito do produto.  
Este SDK é, portanto, não adequado para o tipo de Watch ou da mesma forma dispositivos restritos.

## <a name="Setup"></a>Configuração e pré-requisitos

Este guia assume que criou um back-end com uma tabela. Este guia assume que a tabela tem o mesmo esquema que as tabelas nesses tutoriais. Este guia assume também que, no seu código, referencie `MicrosoftAzureMobile.framework` e importar `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Como: Criar cliente

Para aceder a um back-end de aplicações móveis do Azure no seu projeto, crie um `MSClient`. Substitua `AppUrl` com o URL da aplicação. Pode deixar `gatewayURLString` e `applicationKey` vazio. Se configurar um gateway para a autenticação, preencher `gatewayURLString` com o URL do gateway.

**Objective-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Como: Criar a referência de tabela

Para atualizar ou aceder a dados, crie uma referência para a tabela de back-end. Substitua `TodoItem` pelo nome da sua tabela

**Objective-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>Como: Consultar dados

Para criar uma consulta de base de dados, consultar o `MSTable` objeto. A seguinte consulta obtém todos os itens no `TodoItem` e regista o texto de cada item.

**Objective-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="filtering"></a>Como: Filtro devolveu dados

Para filtrar os resultados, existem muitas opções disponíveis.

Para utilizar um predicado de filtro, utilize um `NSPredicate` e `readWithPredicate`. Os seguintes filtros devolveu dados para localizar apenas os itens de afazeres incompletos.

**Objective-C**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="query-object"></a>Como: Utilizar o MSQuery

Para executar uma consulta complexa (incluindo a classificação e paginação), crie um `MSQuery` de objeto, diretamente ou ao utilizar um predicado:

**Objective-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` permite-lhe controlar diversos comportamentos de consulta.

* Especificar a ordem de resultados
* Limitar os campos a devolver
* Limitar a quantidade de registos a devolver
* Especificar contagem total na resposta
* Especificar parâmetros de cadeia de caracteres de consulta personalizada no pedido
* Aplicar funções adicionais

Executar uma `MSQuery` consulta chamando `readWithCompletion` no objeto.

## <a name="sorting"></a>Como: Classifique dados com o MSQuery

Para ordenar os resultados, vamos examinar um exemplo. Para ordenar por campo 'text' em ordem crescente, em seguida, por descendentes de "completo", invocar `MSQuery` da seguinte forma:

**Objective-C**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="selecting"></a><a name="parameters"></a>Como: Limitar os campos e expanda os parâmetros de cadeia de caracteres de consulta com o MSQuery

Para limitar os campos a serem retornados numa consulta, especifique os nomes dos campos no **selectFields** propriedade. Este exemplo retorna apenas o texto e os campos concluídos:

**Objective-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```swift
query.selectFields = ["text", "complete"]
```

Para incluir parâmetros de cadeia de caracteres de consulta adicionais no pedido de servidor (por exemplo, uma vez que um script personalizado do lado do servidor usa), preencher `query.parameters` da seguinte forma:

**Objective-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Como: Configurar o tamanho da página

Com o Azure Mobile Apps, o tamanho da página controla o número de registos que são obtidos em vez das tabelas de back-end. Uma chamada para `pull` dados seriam do batch, em seguida, segurança de dados, com base neste tamanho de página, até que não há mais registros para extrair.

É possível configurar um tamanho de página usando **MSPullSettings** conforme mostrado abaixo. O tamanho de página predefinido é 50 e o exemplo a seguir altera-a para 3.

É possível configurar um tamanho de página diferente por motivos de desempenho. Se tiver um grande número de registos de dados pequenas, um tamanho de página elevada reduz o número de viagens, no servidor.

Esta definição controla o tamanho de página no lado do cliente. Se o cliente solicita um tamanho de página maior do que suporta o back-end de aplicações móveis, o tamanho da página está limitado ao máximo que o back-end está configurado para suportar.

Esta definição é também o *número* de registos de dados, não a *tamanho em bytes*.

Se aumentar o tamanho da página de cliente, também deve aumentar o tamanho da página no servidor. Consulte ["como: Ajustar o tamanho de paginação de tabela"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter os passos fazer isso.

**Objective-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Como: Inserir dados

Para inserir uma nova linha de tabela, crie uma `NSDictionary` e invocar `table insert`. Se [esquema dinâmico] é ativada, o back-end móvel do serviço de aplicações do Azure gera automaticamente novas colunas com base no `NSDictionary`.

Se `id` não for fornecido, o back-end gera automaticamente um novo ID exclusivo. Fornecer seu próprio `id` para utilizar o e-mail endereços, nomes de utilizador, ou seu próprio valores como ID. Pode diminuir a fornecer seu próprio ID de associações e lógica de negócios e orientados a base de dados.

O `result` contém o item novo que foi inserido. Dependendo de sua lógica de servidor, pode ter dados adicionais ou modificados em comparação com o que foi passado para o servidor.

**Objective-C**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>Como: Modificar dados

Para atualizar uma linha existente, modifique um item e a chamada `update`:

**Objective-C**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Em alternativa, forneça o ID de linha e do campo atualizado:

**Objective-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

No mínimo, o `id` atributo tem de ser definido quando fazer atualizações.

## <a name="deleting"></a>Como: Eliminar dados

Para eliminar um item, invocar `delete` com o item:

**Objective-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Em alternativa, elimine, fornecendo um ID de linha:

**Objective-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

No mínimo, o `id` atributo tem de ser definido quando elimina tomada.

## <a name="customapi"></a>Como: Chamar a API personalizada

Uma API personalizada, pode expor qualquer funcionalidade de back-end. Não tem de mapear para uma operação de tabela. Não só obterá mais controle sobre mensagens, pode mesmo leitura/conjunto de cabeçalhos e alterar o formato do corpo de resposta. Para saber como criar uma API personalizada num back-end, leia [APIs personalizadas](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Para chamar uma API personalizada, chamar `MSClient.invokeAPI`. O pedido e resposta de conteúdo são tratadas como JSON. Para utilizar outros tipos de mídia [utilize a outra sobrecarga do `invokeAPI` ] [ 5].  Para tornar uma `GET` pedir, em vez de um `POST` pedir, parâmetro de conjunto `HTTPMethod` para `"GET"` e o parâmetro `body` para `nil` (uma vez que solicitações GET não têm corpos de mensagem.) Se a API personalizada oferece suporte a outros verbos HTTP, altere `HTTPMethod` adequadamente.

**Objective-C**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="templates"></a>Como: Registe-se os modelos de push para enviar notificações multiplataformas

Para registar modelos, passe modelos com o seu **client.push registerDeviceToken** método na sua aplicação de cliente.

**Objective-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Os modelos são do tipo NSDictionary e podem conter vários modelos no seguinte formato:

**Objective-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Todas as etiquetas são removidas do pedido de segurança.  Para adicionar etiquetas a instalações ou vários modelos dentro de instalações, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure][4].  Para enviar notificações usando esses modelos registados, trabalhar com [APIs de Hubs de notificação][3].

## <a name="errors"></a>Como: Lidar com erros

Quando chama um back-end móvel do App Service do Azure, o bloco de conclusão contém um `NSError` parâmetro. Quando ocorre um erro, este parâmetro é não nil. Em seu código, deve verificar este parâmetro e lidar com o erro, conforme necessário, como demonstrado em fragmentos de código anteriores.

O ficheiro [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] define as constantes `MSErrorResponseKey`, `MSErrorRequestKey`, e `MSErrorServerItemKey`. Para obter mais dados relacionados com o erro:

**Objective-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Além disso, o arquivo define constantes para cada código de erro:

**Objective-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Como: Autenticar utilizadores com o Active Directory Authentication Library

Pode utilizar o Active Directory Authentication Library (ADAL) para iniciar sessão de utilizadores na sua aplicação utilizando o Azure Active Directory. Autenticação de fluxo de cliente através de um fornecedor de identidade SDK é preferível a utilizar o `loginWithProvider:completion:` método.  Autenticação de fluxo de cliente fornece uma noção da experiência do Usuário mais nativa e permite a personalização adicional.

1. Configurar o seu back-end de aplicação móvel para o início de sessão do AAD ao seguir a [como configurar o serviço de aplicações para início de sessão do Active Directory] [ 7] tutorial. Certifique-se concluir o passo opcional de registar uma aplicação cliente nativa. Para iOS, recomendamos que o URI é o formato de redirecionamento `<app-scheme>://<bundle-id>`. Para obter mais informações, consulte a [guia de introdução do iOS da ADAL][8].
2. Instale a ADAL com o Cocoapods. Editar Podfile para incluir a definição seguinte, substituindo **seu PROJETO** com o nome do seu projeto Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   e o Pod:

        pod 'ADALiOS'

3. O terminal, execute `pod install` do diretório que contém seu projeto e, em seguida, abra a área de trabalho do Xcode gerada (não o projeto).
4. Adicione o seguinte código ao seu aplicativo, de acordo com o idioma que está a utilizar. Em cada uma, fazer estas substituições:

   * Substitua **INSERT-autoridade-HERE** com o nome do inquilino que aprovisionou seu aplicativo. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Este valor pode ser copiado da guia no Azure Active Directory no domínio a [portal do Azure].
   * Substitua **INSERT-RESOURCE-ID-HERE** com o ID de cliente para o back-end de aplicação móvel. Pode obter o ID de cliente do **avançadas** separador sob **definições de diretório do Azure Active Directory** no portal.
   * Substitua **INSERT-CLIENT-ID-HERE** com o ID de cliente que copiou da aplicação cliente nativa.
   * Substitua **INSERT-REDIRECIONAMENTO-URI-HERE** com o seu site */.auth/login/done* ponto de extremidade, usando o esquema HTTPS. Este valor deve ser semelhante à *https://contoso.azurewebsites.net/.auth/login/done* .

**Objective-C**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Swift**:

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="facebook-sdk"></a>Como: Autenticar utilizadores com o SDK do Facebook para iOS

Pode utilizar o SDK do Facebook para iOS para inscrever-se os utilizadores na sua aplicação utilizando o Facebook.  Usar uma autenticação de fluxo de cliente é preferível a utilizar o `loginWithProvider:completion:` método.  A autenticação de fluxo de cliente fornece uma noção da experiência do Usuário mais nativa e permite a personalização adicional.

1. Configurar o seu back-end de aplicação móvel para o início de sessão do Facebook ao seguir a [como configurar o serviço de aplicações para início de sessão do Facebook] [ 9] tutorial.
2. Instalar o SDK do Facebook para iOS ao seguir a [SDK do Facebook para iOS - introdução] [ 10] documentação. Em vez de criar uma aplicação, pode adicionar a plataforma iOS para o registo existente.
3. Documentação do Facebook inclui algum código Objective-C no representante do aplicativo. Se estiver a utilizar **Swift**, pode utilizar as seguintes traduções para Appdelegate:

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. Além de adicionar `FBSDKCoreKit.framework` ao seu projeto, também adicionar uma referência ao `FBSDKLoginKit.framework` da mesma forma.
5. Adicione o seguinte código ao seu aplicativo, de acordo com o idioma que está a utilizar.

    **Objective-C**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Swift**:

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="twitter-fabric"></a>Como: Autenticar utilizadores com recursos de infraestrutura do Twitter para iOS

Pode utilizar recursos de infraestrutura para iOS para inscrever-se os utilizadores na sua aplicação utilizando o Twitter. Autenticação de fluxo de cliente é preferível a utilizar o `loginWithProvider:completion:` método, como ele fornece uma noção da experiência do Usuário mais nativa e permite a personalização adicional.

1. Configurar o seu back-end de aplicação móvel para início de sessão no Twitter ao seguir a [como configurar o serviço de aplicações para início de sessão do Twitter](../app-service/configure-authentication-provider-twitter.md) tutorial.
2. Adicionar recursos de infraestrutura ao seu projeto ao seguir a [Recursos de infraestrutura para iOS - introdução] documentação e a configuração TwitterKit.

   > [!NOTE]
   > Por predefinição, os recursos de infraestrutura cria um aplicativo do Twitter para. Pode evitar a criação de um aplicativo registrando-se a chave de consumidor e o segredo de consumidor que criou anteriormente com os seguintes fragmentos de código.    Em alternativa, pode substituir os valores de chave de consumidor e o segredo de consumidor que fornecem ao serviço de aplicações com os valores que pode ver na [Dashboard de recursos de infraestrutura]. Se escolher esta opção, certifique-se de que definir o URL de retorno de chamada para um valor de marcador de posição, tal como `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Se optar por utilizar os segredos que criou anteriormente, adicione o seguinte código para o seu representante do aplicativo:

    **Objective-C**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Swift**:

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. Adicione o seguinte código ao seu aplicativo, de acordo com o idioma que está a utilizar.

    **Objective-C**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Swift**:

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="google-sdk"></a>Como: Autenticar utilizadores com o SDK de início de sessão do Google para iOS

Pode utilizar o SDK de início de sessão do Google para iOS para inscrever-se os utilizadores na sua aplicação utilizando uma conta do Google.  Google recentemente anunciámos alterações às suas políticas de segurança de OAuth.  Estas alterações de política irão exigir a utilização do SDK do Google no futuro.

1. Configurar o seu back-end de aplicação móvel para início de sessão no Google ao seguir a [como configurar o serviço de aplicações para início de sessão do Google](../app-service/configure-authentication-provider-google.md) tutorial.
2. Instalar o SDK do Google para iOS ao seguir a [Google início de sessão para iOS - iniciar a integração de](https://developers.google.com/identity/sign-in/ios/start-integrating) documentação. Pode ignorar a seção "Autenticar com o servidor de back-end de".
3. Adicione o seguinte ao seu delegado `signIn:didSignInForUser:withError:` método, de acordo com o idioma que está a utilizar.

    **Objective-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Certifique-se de que também adiciona o seguinte procedimento para `application:didFinishLaunchingWithOptions:` no seu representante do aplicativo, substituindo "SERVER_CLIENT_ID" com o mesmo ID que utilizou para configurar o serviço de aplicações no passo 1.

    **Objective-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Adicione o seguinte código ao seu aplicativo num UIViewController que implementa o `GIDSignInUIDelegate` protocolo, de acordo com o idioma que está a utilizar.  Ter terminado sessão antes de serem assinados novamente e, embora não precisa de introduzir as suas credenciais novamente, verá uma caixa de diálogo de consentimento.  Chame esse método quando o token de sessão expirou.

   **Objective-C**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Swift**:

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Início rápido de aplicações móveis do Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Portal do Azure]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Esquema dinâmico]: https://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Dashboard de recursos de infraestrutura]: https://www.fabric.io/home
[Recursos de infraestrutura para iOS - introdução]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
