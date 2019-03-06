---
title: Adicionar a autenticação no iOS com aplicações móveis do Azure
description: Saiba como utilizar aplicações móveis do Azure para autenticar os utilizadores da sua aplicação iOS através de uma variedade de fornecedores de identidade, incluindo o AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: crdun
ms.openlocfilehash: 8c1c52790065015977add7e32a06063057b24dad
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445913"
---
# <a name="add-authentication-to-your-ios-app"></a>Adicionar autenticação à sua aplicação iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Neste tutorial, adicionar autenticação para o [início rápido do iOS] através de um fornecedor de identidade com suporte do projeto. Este tutorial baseia-se no [início rápido do iOS] tutorial, o que tem de concluir primeiro.

## <a name="register"></a>Registar a sua aplicação para autenticação e configurar o serviço de aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitidos

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação.  Isso permite que o sistema de autenticação redirecionar para a sua aplicação, uma vez concluído o processo de autenticação.  Neste tutorial, utilizamos o esquema de URL _appname_ em todo.  No entanto, pode utilizar qualquer esquema de URL que escolher.  Deve ser exclusivo para a sua aplicação móvel.  Para ativar o redirecionamento no lado do servidor de th:

1. Na [portal do Azure], selecione o serviço de aplicações.

2. Clique nas **autenticação / autorização** opção de menu.

3. Clique em **do Azure Active Directory** sob a **provedores de autenticação** secção.

4. Definir o **modo de gestão** ao **avançadas**.

5. Na **permitido URLs de redirecionamento externo**, introduza `appname://easyauth.callback`.  O _appname_ nessa cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir normal especificação de URL para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia de caracteres que escolha, como precisará ajustar o código da aplicação móvel com o esquema de URL em vários locais.

6. Clique em **OK**.

7. Clique em **Guardar**.

## <a name="permissions"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Xcode, prima **executar** para iniciar a aplicação. Uma exceção ocorre porque a aplicação tenta acessar o back-end como um utilizador não autenticado, mas a *TodoItem* tabela agora requer autenticação.

## <a name="add-authentication"></a>Adicionar autenticação à aplicação
**Objective-C**:

1. No Mac, abra *QSTodoListViewController.m* no Xcode e adicione o seguinte método:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Alteração *google* ao *microsoftaccount*, *twitter*, *facebook*, ou *windowsazureactivedirectory* se não estiver a utilizar o Google como o fornecedor de identidade. Se usar o Facebook, deve [domínios de Facebook da lista branca] [ 1] na sua aplicação.

    Substitua a **urlScheme** com um nome exclusivo para a sua aplicação.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL que especificou na **permitido URLs de redirecionamento externo** campo no portal do Azure. O urlScheme é utilizada pelo retorno de chamada de autenticação para mudar para a sua aplicação depois do pedido de autenticação está concluído.

2. Substitua `[self refresh]` no `viewDidLoad` na *QSTodoListViewController.m* com o código a seguir:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Abra o `QSAppDelegate.h` de ficheiros e adicione o seguinte código:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Abra o `QSAppDelegate.m` de ficheiros e adicione o seguinte código:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Adicione este código imediatamente antes da leitura de linha `#pragma mark - Core Data stack`.  Substitua a _appname_ com o valor de urlScheme que utilizou no passo 1.

5. Abra o `AppName-Info.plist` ficheiro (AppName substituição com o nome da sua aplicação) e adicione o seguinte código:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Esse código deve ser colocado dentro do `<dict>` elemento.  Substitua a _appname_ cadeia de caracteres (dentro da matriz para **CFBundleURLSchemes**) com o nome da aplicação que selecionou no passo 1.  Pode também efetuar estas alterações no ficheiro plist editor - clique no `AppName-Info.plist` ficheiro no XCode para abrir o editor de plist.

    Substitua a `com.microsoft.azure.zumo` para de cadeias de caracteres **CFBundleURLName** com seu Apple identificador do pacote.

6. Prima *executar* para iniciar a aplicação e, em seguida, iniciar sessão. Quando estiver conectado, deve conseguir ver a lista de tarefas e fazer atualizações.

**Swift**:

1. No Mac, abra *ToDoTableViewController.swift* no Xcode e adicione o seguinte método:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Alteração *google* ao *microsoftaccount*, *twitter*, *facebook*, ou *windowsazureactivedirectory* se não estiver a utilizar o Google como o fornecedor de identidade. Se usar o Facebook, deve [domínios de Facebook da lista branca] [ 1] na sua aplicação.

    Substitua a **urlScheme** com um nome exclusivo para a sua aplicação.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL que especificou na **permitido URLs de redirecionamento externo** campo no portal do Azure. O urlScheme é utilizada pelo retorno de chamada de autenticação para mudar para a sua aplicação depois do pedido de autenticação está concluído.

2. Remover as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` no final do `viewDidLoad()` no *ToDoTableViewController.swift*. Adicione uma chamada para `loginAndGetData()` em seu lugar:

    ```swift
    loginAndGetData()
    ```

3. Abra o `AppDelegate.swift` de ficheiros e adicione a seguinte linha para o `AppDelegate` classe:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Substitua a _appname_ com o valor de urlScheme que utilizou no passo 1.

4. Abra o `AppName-Info.plist` ficheiro (AppName substituição com o nome da sua aplicação) e adicione o seguinte código:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Esse código deve ser colocado dentro do `<dict>` elemento.  Substitua a _appname_ cadeia de caracteres (dentro da matriz para **CFBundleURLSchemes**) com o nome da aplicação que selecionou no passo 1.  Pode também efetuar estas alterações no ficheiro plist editor - clique no `AppName-Info.plist` ficheiro no XCode para abrir o editor de plist.

    Substitua a `com.microsoft.azure.zumo` para de cadeias de caracteres **CFBundleURLName** com seu Apple identificador do pacote.

5. Prima *executar* para iniciar a aplicação e, em seguida, iniciar sessão. Quando estiver conectado, deve conseguir ver a lista de tarefas e fazer atualizações.

Autenticação do serviço de aplicações utiliza a comunicação de Inter-aplicação de maçãs.  Para obter mais detalhes sobre esse assunto, consulte o [documentação da Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Portal do Azure]: https://portal.azure.com

[início rápido do iOS]: app-service-mobile-ios-get-started.md

