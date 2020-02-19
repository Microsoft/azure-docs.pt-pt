---
title: Adicionar Autenticação no iOS
description: Saiba como usar as Aplicações Móveis Azure para autenticar os utilizadores da sua aplicação iOS através de fornecedores de identidade como AAD, Google, Facebook, Twitter e Microsoft.
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fd7860053e8c04ca9d5e355a721afd834835a441
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459028"
---
# <a name="add-authentication-to-your-ios-app"></a>Adicione a autenticação à sua aplicação iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Neste tutorial, adiciona autenticação ao projeto de arranque rápido do [iOS arranque rápido] utilizando um fornecedor de identidade suportado. Este tutorial baseia-se no tutorial de arranque rápido do [iOS arranque rápido] que deve completar primeiro.

## <a name="register"></a>Registe a sua aplicação para autenticação e configure o Serviço de Aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicione a sua aplicação aos URLs De Redirecionamento Externo Permitidos

A autenticação segura requer que defina um novo esquema de URL para a sua aplicação.  Isto permite que o sistema de autenticação redirecione para a sua aplicação uma vez concluído o processo de autenticação.  Neste tutorial, usamos o _nome_ de aplicativo do esquema URL em toda a parte.  No entanto, pode utilizar qualquer esquema de URL que escolha.  Deve ser exclusivo da sua aplicação móvel.  Para ativar a reorientação do lado do servidor th:

1. No [Portal do Azure]selecione o seu Serviço de Aplicações.

2. Clique na opção de **menu Autenticação/Autorização.**

3. Clique no **Diretório Ativo Azure** na secção Fornecedores de **Autenticação.**

4. Desajuste o **modo de Gestão** para **Avançado**.

5. Nos **URLs de Redirecionamento Externo Permitidos,** introduza `appname://easyauth.callback`.  O _nome de aplicação_ nesta cadeia é o URL Scheme para a sua aplicação móvel.  Deve seguir a especificação normal do URL para um protocolo (utilize apenas letras e números, e comece com uma letra).  Deve tomar nota da corda que escolher, pois terá de ajustar o seu código de aplicação móvel com o URL Scheme em vários locais.

6. Clique em **OK**.

7. Clique em **Guardar**.

## <a name="permissions"></a>Restringir permissões a utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Xcode, prima **Run** para iniciar a aplicação. Uma exceção é levantada porque a aplicação tenta aceder ao backend como um utilizador não autenticado, mas a tabela *TodoItem* agora requer autenticação.

## <a name="add-authentication"></a>Adicionar autenticação à app
**Objetivo C:**

1. No seu Mac, abra *o QSTodoListViewController.m* em Xcode e adicione o seguinte método:

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

    Mude o *Google* para *a microsoftaccount*, *twitter*, *facebook*ou *windowsazureactivedirecty* se não estiver a usar o Google como seu fornecedor de identidade. Se utilizar o Facebook, terá de [whitelist os domínios do Facebook][1] na sua aplicação.

    Substitua o **urlScheme** por um nome único para a sua aplicação.  O urlScheme deve ser o mesmo que o protocolo url scheme que especificou no campo **de URLs de Redirecionamento Externo Permitido** no portal Azure. O urlScheme é utilizado pela chamada de autenticação para voltar à sua aplicação depois de o pedido de autenticação estar completo.

2. Substitua `[self refresh]` em `viewDidLoad` em *QSTodoListViewController.m* com o seguinte código:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Abra o ficheiro `QSAppDelegate.h` e adicione-o ao seguinte código:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Abra o ficheiro `QSAppDelegate.m` e adicione-o ao seguinte código:

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

   Adicione este código diretamente antes de a linha de leitura `#pragma mark - Core Data stack`.  Substitua o nome de _aplicação_ pelo valor urlScheme que utilizou no passo 1.

5. Abra o ficheiro `AppName-Info.plist` (substituindo o Nome do App com o nome da sua aplicação) e adicione o seguinte código:

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

    Este código deve ser colocado dentro do elemento `<dict>`.  Substitua a cadeia de _nomes_ de aplicativos (dentro da matriz para **CFBundleURLSchemes)** com o nome da aplicação que escolheu no passo 1.  Também pode efazer estas alterações no editor da lista - clique no ficheiro `AppName-Info.plist` no XCode para abrir o editor da lista.

    Substitua a cadeia `com.microsoft.azure.zumo` por **CFBundleURLName** pelo seu identificador apple bundle.

6. Pressione *Corra* para iniciar a aplicação e, em seguida, faça login. Quando estiver em sessão, deverá ser capaz de ver a lista Todo e fazer atualizações.

**Swift:**

1. No seu Mac, abra *o ToDoTableViewController.swift* em Xcode e adicione o seguinte método:

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

    Mude o *Google* para *a microsoftaccount*, *twitter*, *facebook*ou *windowsazureactivedirecty* se não estiver a usar o Google como seu fornecedor de identidade. Se utilizar o Facebook, terá de [whitelist os domínios do Facebook][1] na sua aplicação.

    Substitua o **urlScheme** por um nome único para a sua aplicação.  O urlScheme deve ser o mesmo que o protocolo url scheme que especificou no campo **de URLs de Redirecionamento Externo Permitido** no portal Azure. O urlScheme é utilizado pela chamada de autenticação para voltar à sua aplicação depois de o pedido de autenticação estar completo.

2. Remova as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` no final da `viewDidLoad()` *toDoTableViewController.swift*. Adicione uma chamada para `loginAndGetData()` no seu lugar:

    ```swift
    loginAndGetData()
    ```

3. Abra o ficheiro `AppDelegate.swift` e adicione a seguinte linha à classe `AppDelegate`:

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

    Substitua o nome de _aplicação_ pelo valor urlScheme que utilizou no passo 1.

4. Abra o ficheiro `AppName-Info.plist` (substituindo o Nome do App com o nome da sua aplicação) e adicione o seguinte código:

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

    Este código deve ser colocado dentro do elemento `<dict>`.  Substitua a cadeia de _nomes_ de aplicativos (dentro da matriz para **CFBundleURLSchemes)** com o nome da aplicação que escolheu no passo 1.  Também pode efazer estas alterações no editor da lista - clique no ficheiro `AppName-Info.plist` no XCode para abrir o editor da lista.

    Substitua a cadeia `com.microsoft.azure.zumo` por **CFBundleURLName** pelo seu identificador apple bundle.

5. Pressione *Corra* para iniciar a aplicação e, em seguida, faça login. Quando estiver em sessão, deverá ser capaz de ver a lista Todo e fazer atualizações.

A autenticação do serviço de aplicações utiliza a Comunicação Inter-App Apples.  Para mais detalhes sobre este assunto, consulte a [Documentação][2] da Apple
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Portal do Azure]: https://portal.azure.com

[iOS arranque rápido]: app-service-mobile-ios-get-started.md

