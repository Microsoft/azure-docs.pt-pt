---
title: Utilize o iOS SDK
description: Como utilizar o iOS SDK para aplicações móveis Azure
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1bf8f8e198f6c4a4a0af308262cd830685698a80
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458924"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Como utilizar a biblioteca de clientes iOS para aplicações móveis Azure

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Descrição geral
Este guia ensina-o a realizar cenários comuns utilizando as mais recentes [Aplicações Móveis Azure iOS SDK][1]. Se você é novo em Aplicativos Móveis Azure, primeiro complete [Aplicativos móveis Azure Arranque rápido] para criar um backend, criar uma tabela e baixar um projeto de iOS Xcode pré-construído. Neste guia, focamo-nos no iOS SDK do lado do cliente. Para saber mais sobre o SDK do lado do servidor para o backend, consulte o Server SDK HOWTOs.

## <a name="reference-documentation"></a>Documentação de referência

A documentação de referência para o cliente iOS SDK está localizada aqui: [Azure Mobile Apps iOS Client Reference][2].

## <a name="supported-platforms"></a>Plataformas Suportadas

O iOS SDK apoia projetos Objective-C, swift 2.2 e swift 2.3 projetos para as versões iOS 8.0 ou posteriores.

A autenticação "server-flow" utiliza um WebView para o UI apresentado.  Se o dispositivo não conseguir apresentar um WebView UI, então é necessário outro método de autenticação que esteja fora do âmbito do produto.  
Este SDK não é, portanto, adequado para dispositivos do tipo Watch ou similarmente restritos.

## <a name="Setup"></a>Configuração e Pré-requisitos

Este guia assume que criou um backend com uma mesa. Este guia assume que a mesa tem o mesmo esquema que as mesas nesses tutoriais. Este guia também assume que no seu código, você refere `MicrosoftAzureMobile.framework` e importa `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Como: Criar cliente

Para aceder a um backend de aplicações móveis Azure no seu projeto, crie uma `MSClient`. Substitua `AppUrl` com o URL da aplicação. Pode deixar `gatewayURLString` e `applicationKey` vazio. Se configurar uma porta de entrada para autenticação, preencha `gatewayURLString` com o URL do portal.

**Objetivo C:**

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift:**

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Como: Criar Referência de Tabela

Para atualizar ou aceder a dados, crie uma referência para a tabela de back-end. Substitua `TodoItem` pelo nome da sua tabela

**Objetivo C:**

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift:**

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>Como: Dados de Consulta

Para criar uma consulta de base de dados, questione o `MSTable` objeto. A seguinte consulta recebe todos os itens em `TodoItem` e regista o texto de cada item.

**Objetivo C:**

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

**Swift:**

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

## <a name="filtering"></a>Como: Filtrar dados devolvidos

Para filtrar os resultados, existem muitas opções disponíveis.

Para filtrar utilizando um predicado, utilize uma `NSPredicate` e `readWithPredicate`. Os filtros seguintes devolveram dados para encontrar apenas itens Todo incompletos.

**Objetivo C:**

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

**Swift:**

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

Para realizar uma consulta complexa (incluindo triagem e pagagem), crie um `MSQuery` objeto, diretamente ou utilizando um predicado:

**Objetivo C:**

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift:**

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` permite controlar vários comportamentos de consulta.

* Especificar ordem dos resultados
* Limite quais os campos a devolver
* Limite quantos registos devolver
* Especificar contagem total em resposta
* Especifique parâmetros de cadeia de consulta personalizadas a pedido
* Aplicar funções adicionais

Execute uma consulta `MSQuery` chamando `readWithCompletion` sobre o objeto.

## <a name="sorting"></a>Como: Ordenar dados com MSQuery

Para classificar os resultados, vamos olhar para um exemplo. Para ordenar por campo "texto" ascendente, em seguida, por "completo" descendente, invoque `MSQuery` assim:

**Objetivo C:**

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

**Swift:**

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

## <a name="selecting"></a><a name="parameters"></a>Como: Limitar campos e expandir parâmetros de cordas de consulta com MSQuery

Para limitar os campos a serem devolvidos numa consulta, especifique os nomes dos campos na propriedade **selectFields.** Este exemplo devolve apenas o texto e os campos concluídos:

**Objetivo C:**

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift:**

```swift
query.selectFields = ["text", "complete"]
```

Para incluir parâmetros de cadeia de consulta adicionais no pedido do servidor (por exemplo, porque um script personalizado do lado do servidor os utiliza), povoe `query.parameters` assim:

**Objetivo C:**

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift:**

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Como configurar o tamanho da página

Com as Aplicações Móveis Azure, o tamanho da página controla o número de registos que são puxados de cada vez a partir das tabelas de backend. Uma chamada para `pull` dados iria então reunir dados, com base no tamanho desta página, até que não haja mais registos para puxar.

É possível configurar um tamanho de página usando **MSPullSettings** como mostrado abaixo. O tamanho da página padrão é de 50, e o exemplo abaixo muda-o para 3.

Pode configurar um tamanho de página diferente por razões de desempenho. Se tiver um grande número de pequenos registos de dados, um tamanho de página alta reduz o número de viagens de ida e volta do servidor.

Esta definição controla apenas o tamanho da página no lado do cliente. Se o cliente pedir um tamanho de página maior do que o suporte de backend das Aplicações Móveis, o tamanho da página está limitado no máximo que o backend está configurado para suportar.

Esta definição é também o *número* de registos de dados, não o tamanho do *byte*.

Se aumentar o tamanho da página do cliente, também deverá aumentar o tamanho da página no servidor. Consulte ["Como: Ajustar o tamanho da tabela"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para os passos para o fazer.

**Objetivo C:**

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift:**

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Como: Inserir Dados

Para inserir uma nova linha de mesa, crie um `NSDictionary` e invoque `table insert`. Se o [Schema Dinâmico] estiver ativado, o backend móvel do Azure App Service gera automaticamente novas colunas com base no `NSDictionary`.

Se `id` não for fornecida, o backend gera automaticamente um novo ID único. Forneça o seu próprio `id` para usar endereços de e-mail, nomes de utilizador ou os seus próprios valores personalizados como ID. Fornecer o seu próprio ID pode facilitar a adesão e lógica de base de dados orientada para o negócio.

O `result` contém o novo item que foi inserido. Dependendo da lógica do servidor, pode ter dados adicionais ou modificados em comparação com o que foi passado para o servidor.

**Objetivo C:**

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

**Swift:**

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

## <a name="modifying"></a>Como: Modificar Dados

Para atualizar uma linha existente, modifique um item e ligue `update`:

**Objetivo C:**

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

**Swift:**

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

Alternativamente, forneça o ID da linha e o campo atualizado:

**Objetivo C:**

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift:**

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

No mínimo, o atributo `id` deve ser definido ao fazer atualizações.

## <a name="deleting"></a>Como: Eliminar Dados

Para apagar um item, invoque `delete` com o item:

**Objetivo C:**

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift:**

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Em alternativa, elimine fornecendo uma identificação de linha:

**Objetivo C:**

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift:**

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

No mínimo, o atributo `id` deve ser definido ao efazer as exclusões.

## <a name="customapi"></a>Como: Chamar API personalizado

Com uma API personalizada, pode expor qualquer funcionalidade de backend. Não tem de mapear uma operação à mesa. Não só ganha sondar mais sobre as mensagens, como pode até ler/definir cabeçalhos e alterar o formato do corpo de resposta.

Para chamar uma API personalizada, ligue para `MSClient.invokeAPI`. O conteúdo de pedido e resposta é tratado como JSON. Para utilizar outros tipos de meios, [utilize a outra sobrecarga de `invokeAPI`][5].  Para fazer um pedido de `GET` em vez de um pedido de `POST`, defina `HTTPMethod` de parâmetros para `"GET"` e parâmetro `body` para `nil` (uma vez que os pedidos do GET não têm corpos de mensagens.) Se a sua API personalizada suportar outros verbos HTTP, mude `HTTPMethod` adequadamente.

**Objetivo C:**

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

**Swift:**

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

## <a name="templates"></a>Como: Registar modelos de empurrar para enviar notificações cross-platform

Para registar modelos, passe modelos com o seu **cliente.push registerDeviceToken** método na sua aplicação de cliente.

**Objetivo C:**

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift:**

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Os seus modelos são de NSDictionary tipo e podem conter vários modelos no seguinte formato:

**Objetivo C:**

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift:**

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Todas as etiquetas são retiradas do pedido de segurança.  Para adicionar etiquetas a instalações ou modelos dentro das instalações, consulte [Trabalhar com o servidor de backend .NET SDK para aplicações móveis Azure][4].  Para enviar notificações utilizando estes modelos registados, trabalhe com APIs de Centros de [Notificação][3].

## <a name="errors"></a>Como: Lidar com erros

Quando chama um backend móvel do Azure App Service, o bloco de conclusão contém um parâmetro `NSError`. Quando ocorre um erro, este parâmetro não é nulo. No seu código, deve verificar este parâmetro e lidar com o erro conforme necessário, conforme demonstrado nos fragmentos de código anteriores.

O ficheiro [`<WindowsAzureMobileServices/MSError.h>`][6] define as constantes `MSErrorResponseKey`, `MSErrorRequestKey`e `MSErrorServerItemKey`. Para obter mais dados relacionados com o erro:

**Objetivo C:**

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift:**

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Além disso, o ficheiro define constantes para cada código de erro:

**Objetivo C:**

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift:**

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Como: Autenticar utilizadores com a Biblioteca de Autenticação do Diretório Ativo

Pode utilizar a Biblioteca de Autenticação de Diretórios Ativos (ADAL) para inscrever os utilizadores na sua aplicação utilizando o Diretório Ativo do Azure. A autenticação do fluxo de clientes utilizando um fornecedor de identidade SDK é preferível a utilizar o método `loginWithProvider:completion:`.  A autenticação do fluxo do cliente proporciona uma sensação UX mais nativa e permite uma personalização adicional.

1. Configure o seu backend de aplicação móvel para iniciar sessão aAD seguindo o Serviço de Aplicações para tutorial de [login de Diretório Ativo.][7] Certifique-se de completar o passo opcional de registar uma aplicação de cliente nativo. Para o iOS, recomendamos que o URI redirecionário seja do formulário `<app-scheme>://<bundle-id>`. Para mais informações, consulte o quickstart da [ADAL iOS][8].
2. Instale o ADAL utilizando cocoapods. Edite o seu Podfile para incluir a seguinte definição, substituindo o **SEU PROJETO** pelo nome do seu projeto Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   e o Pod:

        pod 'ADALiOS'

3. Utilizando o Terminal, execute `pod install` a partir do diretório que contenha o seu projeto e, em seguida, abra o espaço de trabalho gerado xcode (não o projeto).
4. Adicione o seguinte código à sua aplicação, de acordo com o idioma que está a utilizar. Em cada uma, faça estas substituições:

   * Substitua o **INSERT-AUTHORITY-HERE** pelo nome do inquilino em que aprovisionou a sua candidatura. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Este valor pode ser copiado a partir do separador Domínio no seu Diretório Ativo Azure no [Portal do Azure].
   * Substitua o **INSERT-RESOURCE-ID-AQUI** pelo ID do cliente para o seu backend de aplicação móvel. Pode obter o ID do cliente a partir do separador **Advanced** em Definições de **Diretório Ativo Azure** no portal.
   * Substitua o **INSERT-CLIENT-ID-HERE** pelo ID do cliente copiado da aplicação do cliente nativo.
   * Substitua **o INSERT-REDIRECT-URI-HERE** pelo ponto final do seu site */.auth/login/done,* utilizando o esquema HTTPS. Este valor deve ser semelhante ao *https://contoso.azurewebsites.net/.auth/login/done* .

**Objetivo C:**

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

**Swift:**

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

## <a name="facebook-sdk"></a>Como: Autenticar utilizadores com o Facebook SDK para iOS

Pode utilizar o SDK do Facebook para o iOS para iniciar a contratação de utilizadores na sua aplicação através do Facebook.  A utilização de uma autenticação de fluxo de cliente é preferível à utilização do método `loginWithProvider:completion:`.  A autenticação do fluxo do cliente proporciona uma sensação UX mais nativa e permite uma personalização adicional.

1. Configure o seu backend de aplicação móvel para o facebook de login, seguindo o [Serviço de Aplicações para][9] o tutorial de login do Facebook.
2. Instale o Facebook SDK para iOS seguindo o [Facebook SDK para iOS - Iniciar][10] a documentação. Em vez de criar uma aplicação, pode adicionar a plataforma iOS ao seu registo existente.
3. A documentação do Facebook inclui algum código Objective-C no Delegado da Aplicação. Se estiver a utilizar **swift,** pode utilizar as seguintes traduções para AppDelegate.swift:

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
4. Além de adicionar `FBSDKCoreKit.framework` ao seu projeto, adicione também uma referência a `FBSDKLoginKit.framework` da mesma forma.
5. Adicione o seguinte código à sua aplicação, de acordo com o idioma que está a utilizar.

    **Objetivo C:**

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

    **Swift:**

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

## <a name="twitter-fabric"></a>Como: Autenticar utilizadores com Tecido Twitter para iOS

Pode utilizar o Fabric para o iOS para contratar utilizadores na sua aplicação através do Twitter. A autenticação do Fluxo do Cliente é preferível à utilização do método `loginWithProvider:completion:`, uma vez que proporciona uma sensação UX mais nativa e permite uma personalização adicional.

1. Configure o seu backend de aplicação móvel para o twitter de início de sessão, seguindo o [Serviço de Aplicações para](../app-service/configure-authentication-provider-twitter.md) o tutorial de login do Twitter.
2. Adicione tecido ao seu projeto seguindo o [Tecido para iOS - Começar] documentação e configurando o TwitterKit.

   > [!NOTE]
   > Por padrão, a Fabric cria uma aplicação no Twitter para si. Pode evitar criar uma aplicação registando a Chave do Consumidor e a Segredo do Consumidor que criou anteriormente utilizando os seguintes códigos.    Em alternativa, pode substituir os valores da Chave do Consumidor e do Segredo do Consumidor que fornece ao Serviço de Aplicações com os valores que vê no [Painel de Instrumentos de Tecido]de Tecido . Se escolher esta opção, certifique-se de que define o URL de chamada para um valor de espaço reservado, como `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Se optar por utilizar os segredos que criou anteriormente, adicione o seguinte código ao seu Delegado de Aplicações:

    **Objetivo C:**

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

    **Swift:**

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

3. Adicione o seguinte código à sua aplicação, de acordo com o idioma que está a utilizar.

    **Objetivo C:**

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

    **Swift:**

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

## <a name="google-sdk"></a>Como: Autenticar utilizadores com o Google Sign-In SDK para iOS

Pode utilizar o SDK de entrada no Google para o iOS para iniciar sessão de utilizadores na sua aplicação através de uma conta Google.  A Google anunciou recentemente alterações às suas políticas de segurança OAuth.  Estas mudanças de política exigirão a utilização do Google SDK no futuro.

1. Configure o seu backend de aplicação móvel para o google sign-in, seguindo o [Serviço de Aplicações para](../app-service/configure-authentication-provider-google.md) o tutorial de login do Google.
2. Instale o Google SDK para iOS seguindo o [Google Sign-In para iOS - Comece a integrar documentação.](https://developers.google.com/identity/sign-in/ios/start-integrating) Pode saltar a secção "Autenticar com um Servidor de Backend".
3. Adicione o seguinte ao método `signIn:didSignInForUser:withError:` do seu delegado, de acordo com o idioma que está a usar.

    **Objetivo C:**
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift:**

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Certifique-se de que também adiciona o seguinte ao `application:didFinishLaunchingWithOptions:` no seu delegado de aplicações, substituindo "SERVER_CLIENT_ID" pelo mesmo ID que usou para configurar o Serviço de Aplicações no passo 1.

    **Objetivo C:**

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift:**

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Adicione o seguinte código à sua aplicação num UIViewController que implementa o protocolo `GIDSignInUIDelegate`, de acordo com o idioma que está a usar.  Está assinado antes de ser inscrito novamente, e embora não precise de introduzir as suas credenciais novamente, vê um diálogo de consentimento.  Basta chamar este método quando a ficha da sessão expirar.

   **Objetivo C:**

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

   **Swift:**

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
[Aplicativos móveis Azure Arranque rápido]: app-service-mobile-ios-get-started.md

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
[Schema Dinâmico]: https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/dynamic-schema
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Painel de Instrumentos de Tecido]: https://www.fabric.io/home
[Tecido para iOS - Começar]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v2-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
