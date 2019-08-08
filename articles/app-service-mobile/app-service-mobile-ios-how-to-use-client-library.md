---
title: Como usar o SDK do iOS para aplicativos móveis do Azure
description: Como usar o SDK do iOS para aplicativos móveis do Azure
services: app-service\mobile
documentationcenter: ios
author: elamalani
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 898bf082874a1e9bf26dd094a6a0fe55417c9d8e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851071"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Como usar a biblioteca de cliente do iOS para aplicativos móveis do Azure

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center está investindo em serviços novos e integrados central para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem. Confira [app Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=/app-service-mobile-ios-how-to-use-client-library) hoje.
>

## <a name="overview"></a>Descrição geral
Este guia ensina a executar cenários comuns usando o SDK do [Ios dos aplicativos móveis do Azure][1]mais recentes. Se você for novo nos aplicativos móveis do Azure, primeiro conclua os [Início Rápido de aplicativos móveis do Azure] para criar um back-end, criar uma tabela e baixar um projeto do Xcode do IOS criado previamente. Neste guia, nos concentramos no SDK do iOS do lado do cliente. Para saber mais sobre o SDK do lado do servidor para o back-end, consulte o SDK do servidor HOWTO.

## <a name="reference-documentation"></a>Documentação de referência

A documentação de referência para o SDK do cliente do iOS está localizada aqui: [Referência de cliente Ios dos aplicativos móveis do Azure][2].

## <a name="supported-platforms"></a>Plataformas com suporte

O SDK do iOS dá suporte a projetos Objective-C, projetos Swift 2,2 e projetos Swift 2,3 para versões do iOS 8,0 ou posterior.

A autenticação "fluxo de servidor" usa uma WebView para a interface do usuário apresentada.  Se o dispositivo não puder apresentar uma interface do usuário do WebView, será necessário outro método de autenticação que esteja fora do escopo do produto.  
Esse SDK, portanto, não é adequado para o tipo de inspeção ou para dispositivos de forma restrita.

## <a name="Setup"></a>Instalação e pré-requisitos

Este guia pressupõe que você criou um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema que as tabelas nesses tutoriais. Este guia também pressupõe que, em seu código, você `MicrosoftAzureMobile.framework` referencie e `MicrosoftAzureMobile/MicrosoftAzureMobile.h`importe.

## <a name="create-client"></a>Como: Criar cliente

Para acessar um back-end de aplicativos móveis do Azure em seu `MSClient`projeto, crie um. Substituir `AppUrl` pela URL do aplicativo. Você pode deixar `gatewayURLString` e `applicationKey` vazio. Se você configurar um gateway para autenticação, preencha `gatewayURLString` com a URL do gateway.

**Objective-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Como: Criar referência de tabela

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

Para criar uma consulta de banco de dados `MSTable` , consulte o objeto. A consulta a seguir obtém todos os itens `TodoItem` em e registra o texto de cada item.

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

## <a name="filtering"></a>Como: Filtrar dados retornados

Para filtrar os resultados, há muitas opções disponíveis.

Para filtrar usando um predicado, `NSPredicate` use `readWithPredicate`um e. Os filtros a seguir retornaram dados para localizar somente itens de tarefa incompleta.

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

## <a name="query-object"></a>Como: Usar o MSQuery

Para executar uma consulta complexa (incluindo classificação e paginação), `MSQuery` crie um objeto, diretamente ou usando um predicado:

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

`MSQuery`permite controlar vários comportamentos de consulta.

* Especificar a ordem dos resultados
* Limitar quais campos retornar
* Limitar o número de registros a serem retornados
* Especificar contagem total em resposta
* Especificar parâmetros de cadeia de caracteres de consulta personalizada na solicitação
* Aplicar funções adicionais

Execute uma `MSQuery` consulta chamando `readWithCompletion` no objeto.

## <a name="sorting"></a>Como: Classificar dados com o MSQuery

Para classificar os resultados, vejamos um exemplo. Para classificar por ' texto ' de campo em ordem crescente e, em seguida, por ' `MSQuery` concluir ' decrescente, invoque assim:

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

## <a name="selecting"></a><a name="parameters"></a>Como: Limitar campos e expandir parâmetros de cadeia de caracteres de consulta com MSQuery

Para limitar os campos a serem retornados em uma consulta, especifique os nomes dos campos na propriedade **selectFields** . Este exemplo retorna apenas o texto e os campos preenchidos:

**Objective-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```swift
query.selectFields = ["text", "complete"]
```

Para incluir parâmetros de cadeia de caracteres de consulta adicionais na solicitação do servidor (por exemplo, porque um script personalizado do lado do servidor `query.parameters` os usa), preencha desta forma:

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

## <a name="paging"></a>Como: Configurar tamanho da página

Com os aplicativos móveis do Azure, o tamanho da página controla o número de registros que são recebidos por vez das tabelas de back-end. Uma chamada para `pull` dados resultaria em lote os dados, com base nesse tamanho de página, até que não haja mais registros para efetuar pull.

É possível configurar um tamanho de página usando **MSPullSettings** , conforme mostrado abaixo. O tamanho de página padrão é 50 e o exemplo a seguir o altera para 3.

Você pode configurar um tamanho de página diferente por motivos de desempenho. Se você tiver um grande número de registros de dados pequenos, um tamanho de página alto reduzirá o número de viagens de ida e volta do servidor.

Essa configuração controla apenas o tamanho da página no lado do cliente. Se o cliente solicitar um tamanho de página maior do que o back-end dos aplicativos móveis dá suporte, o tamanho da página será limitado no máximo, o back-end será configurado para dar suporte a.

Essa configuração também é o *número* de registros de dados, não o *tamanho do byte*.

Se você aumentar o tamanho da página do cliente, também deverá aumentar o tamanho da página no servidor. Consulte ["como: Ajuste o tamanho da paginação da tabela "](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter as etapas para fazer isso.

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

Para inserir uma nova linha de tabela, crie `NSDictionary` uma e `table insert`invoque. Se o [esquema dinâmico] estiver habilitado, o back-end do serviço de Azure App móvel gerará `NSDictionary`automaticamente novas colunas com base no.

Se `id` não for fornecido, o back-end gerará automaticamente uma nova ID exclusiva. Forneça seu próprio `id` para usar endereços de email, nomes de acessadores ou seus próprios valores personalizados como ID. Fornecer sua própria ID pode facilitar junções e lógica de banco de dados orientada aos negócios.

O `result` contém o novo item que foi inserido. Dependendo da lógica do servidor, ele pode ter dados adicionais ou modificados em comparação com o que foi passado para o servidor.

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

Para atualizar uma linha existente, modifique um item e chame `update`:

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

Como alternativa, forneça a ID da linha e o campo atualizado:

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

No mínimo, o `id` atributo deve ser definido ao fazer atualizações.

## <a name="deleting"></a>Como: Excluir dados

Para excluir um item, invoque `delete` com o item:

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

Como alternativa, exclua fornecendo uma ID de linha:

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

No mínimo, o `id` atributo deve ser definido ao fazer exclusões.

## <a name="customapi"></a>Como: Chamar API personalizada

Com uma API personalizada, você pode expor qualquer funcionalidade de back-end. Ele não precisa ser mapeado para uma operação de tabela. Além de ter mais controle sobre as mensagens, você pode até mesmo ler/definir cabeçalhos e alterar o formato do corpo da resposta.

Para chamar uma API personalizada, chame `MSClient.invokeAPI`. O conteúdo de solicitação e resposta é tratado como JSON. Para usar outros tipos de mídia, [use a outra sobrecarga `invokeAPI`do ][5].  Para fazer uma `GET` solicitação em vez de `POST` uma solicitação, defina `HTTPMethod` o `"GET"` parâmetro para `body` e `nil` o parâmetro como (já que as solicitações GET não têm corpos de mensagens). Se a API personalizada der suporte a outros verbos HTTP, `HTTPMethod` altere adequadamente.

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

## <a name="templates"></a>Como: Registrar modelos de push para enviar notificações entre plataformas

Para registrar modelos, passe modelos com seu **cliente. envie por push** o método registerDeviceToken em seu aplicativo cliente.

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

Seus modelos são do tipo NSDictionary e podem conter vários modelos no seguinte formato:

**Objective-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Todas as marcas são removidas da solicitação de segurança.  Para adicionar marcas a instalações ou modelos em instalações, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure][4].  Para enviar notificações usando esses modelos registrados, trabalhe com [APIs de hubs de notificação][3].

## <a name="errors"></a>Como: Tratar erros

Quando você chama um back-end móvel do serviço de Azure app, o `NSError` bloco de conclusão contém um parâmetro. Quando ocorre um erro, esse parâmetro é não nulo. Em seu código, você deve verificar esse parâmetro e tratar o erro conforme necessário, conforme demonstrado nos trechos de código anteriores.

O arquivo [`<WindowsAzureMobileServices/MSError.h>`][6] define as constantes `MSErrorResponseKey`, `MSErrorRequestKey`e `MSErrorServerItemKey`. Para obter mais dados relacionados ao erro:

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

## <a name="adal"></a>Como: Autenticar usuários com o Biblioteca de Autenticação do Active Directory

Você pode usar o Biblioteca de Autenticação do Active Directory (ADAL) para conectar usuários ao seu aplicativo usando Azure Active Directory. A autenticação de fluxo de cliente usando um SDK do provedor de identidade é `loginWithProvider:completion:` preferível ao uso do método.  A autenticação de fluxo de cliente fornece uma aparência de UX mais nativa e permite personalização adicional.

1. Configure o back-end do aplicativo móvel para entrar no AAD seguindo o tutorial [como configurar o serviço de aplicativo para Active Directory logon][7] . Certifique-se de concluir a etapa opcional de registrar um aplicativo cliente nativo. Para iOS, recomendamos que o URI de redirecionamento esteja `<app-scheme>://<bundle-id>`no formato. Para obter mais informações, consulte o guia de [início rápido do IOS para Adal][8].
2. Instale a ADAL usando Cocoapods. Edite seu Podfile para incluir a definição a seguir, substituindo **seu projeto** pelo nome do seu projeto do Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   e o Pod:

        pod 'ADALiOS'

3. Usando o terminal, execute `pod install` a partir do diretório que contém seu projeto e, em seguida, abra o espaço de trabalho do Xcode gerado (não o projeto).
4. Adicione o código a seguir ao seu aplicativo, de acordo com o idioma que você está usando. Em cada, faça estas substituições:

   * Substitua **Insert-Authority-aqui** pelo nome do locatário no qual você provisionou seu aplicativo. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Esse valor pode ser copiado da guia domínio em seu Azure Active Directory no [Azure portal].
   * Substitua **Insert-Resource-ID-aqui** pela ID do cliente para o back-end do aplicativo móvel. Você pode obter a ID do cliente na guia **avançado** em **configurações de Azure Active Directory** no Portal.
   * Substitua **Inserir-Client-ID-aqui** pela ID do cliente que você copiou do aplicativo cliente nativo.
   * Substitua **Insert-reredirect-URI-aqui** pelo ponto de extremidade */.auth/login/Done* do seu site, usando o esquema HTTPS. Esse valor deve ser semelhante a *https://contoso.azurewebsites.net/.auth/login/done* .

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

## <a name="facebook-sdk"></a>Como: Autenticar usuários com o SDK do Facebook para iOS

Você pode usar o SDK do Facebook para iOS para conectar usuários ao seu aplicativo usando o Facebook.  Usar uma autenticação de fluxo de cliente é preferível `loginWithProvider:completion:` ao uso do método.  A autenticação de fluxo de cliente fornece uma aparência de UX mais nativa e permite personalização adicional.

1. Configure seu back-end de aplicativo móvel para entrada no Facebook seguindo o tutorial [como configurar o serviço de aplicativo para logon no Facebook][9] .
2. Instale o SDK do Facebook para iOS seguindo a documentação [do SDK do Facebook para IOS-introdução][10] . Em vez de criar um aplicativo, você pode adicionar a plataforma iOS ao seu registro existente.
3. A documentação do Facebook inclui um código Objective-C no delegado do aplicativo. Se você estiver usando o **Swift**, poderá usar as seguintes traduções para AppDelegate. Swift:

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
4. Além de adicionar `FBSDKCoreKit.framework` ao seu projeto, adicione também uma referência ao `FBSDKLoginKit.framework` da mesma maneira.
5. Adicione o código a seguir ao seu aplicativo, de acordo com o idioma que você está usando.

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

## <a name="twitter-fabric"></a>Como: Autenticar usuários com a malha do Twitter para iOS

Você pode usar a malha para iOS para conectar usuários ao seu aplicativo usando o Twitter. A autenticação de fluxo de cliente é preferível ao uso do `loginWithProvider:completion:` método, pois fornece uma aparência de UX mais nativa e permite personalização adicional.

1. Configure seu back-end de aplicativo móvel para entrar no Twitter seguindo o tutorial [como configurar o serviço de aplicativo para logon no Twitter](../app-service/configure-authentication-provider-twitter.md) .
2. Adicione a malha ao seu projeto seguindo o [Fabric para iOS-Introdução] e Configurando o TwitterKit.

   > [!NOTE]
   > Por padrão, a malha cria um aplicativo do Twitter para você. Você pode evitar a criação de um aplicativo registrando a chave do consumidor e o segredo do consumidor criados anteriormente usando os trechos de código a seguir.    Como alternativa, você pode substituir a chave do consumidor e os valores de segredo do consumidor que você fornece ao serviço de aplicativo com os valores que você vê no [painel de malha]. Se você escolher essa opção, certifique-se de definir a URL de retorno de chamada para um valor `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`de espaço reservado, como.

    Se você optar por usar os segredos criados anteriormente, adicione o seguinte código ao seu representante de aplicativo:

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

3. Adicione o código a seguir ao seu aplicativo, de acordo com o idioma que você está usando.

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

## <a name="google-sdk"></a>Como: Autenticar usuários com o SDK de entrada do Google para iOS

Você pode usar o SDK de entrada do Google para iOS para conectar usuários ao seu aplicativo usando uma conta do Google.  O Google anunciou recentemente alterações em suas políticas de segurança OAuth.  Essas alterações de política exigirão o uso do SDK do Google no futuro.

1. Configure seu back-end do aplicativo móvel para entrar no Google seguindo o tutorial [como configurar o serviço de aplicativo para o logon do Google](../app-service/configure-authentication-provider-google.md) .
2. Instale o SDK do Google para iOS seguindo a documentação [de entrada do Google para IOS-iniciar integração](https://developers.google.com/identity/sign-in/ios/start-integrating) . Você pode ignorar a seção "autenticar com um servidor back-end".
3. Adicione o seguinte ao método do `signIn:didSignInForUser:withError:` seu representante, de acordo com o idioma que você está usando.

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

4. Certifique-se também de adicionar o seguinte `application:didFinishLaunchingWithOptions:` ao no seu representante do aplicativo, substituindo "SERVER_CLIENT_ID" pela mesma ID que você usou para configurar o serviço de aplicativo na etapa 1.

    **Objective-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Adicione o código a seguir ao seu aplicativo em um UIViewController que implementa `GIDSignInUIDelegate` o protocolo, de acordo com o idioma que você está usando.  Você é desconectado antes de ser conectado novamente e, embora não precise inserir suas credenciais novamente, você verá uma caixa de diálogo de consentimento.  Só chame esse método quando o token de sessão tiver expirado.

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
[Início Rápido de aplicativos móveis do Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure portal]: https://portal.azure.com/
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

[Painel de malha]: https://www.fabric.io/home
[Fabric para iOS-Introdução]: https://docs.fabric.io/ios/fabric/getting-started.html
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
