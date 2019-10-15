---
title: Adicionar autenticação no iOS com aplicativos móveis do Azure
description: Saiba como usar os aplicativos móveis do Azure para autenticar usuários de seu aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 674d5f783f43011ba154b668cea4ec41f6a945f5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025276"
---
# <a name="add-authentication-to-your-ios-app"></a>Adicionar autenticação ao seu aplicativo iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

Neste tutorial, você adicionará autenticação ao projeto de [início rápido do IOS] usando um provedor de identidade com suporte. Este tutorial se baseia no tutorial de [início rápido do IOS] , que você deve concluir primeiro.

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar o serviço de aplicativo
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo.  Isso permite que o sistema de autenticação Redirecione para seu aplicativo após a conclusão do processo de autenticação.  Neste tutorial, usamos o esquema de URL _AppName_ em todo o.  No entanto, você pode usar qualquer esquema de URL que escolher.  Ele deve ser exclusivo para seu aplicativo móvel.  Para habilitar o redirecionamento no lado do servidor:

1. No [Azure portal], selecione o serviço de aplicativo.

2. Clique na opção de menu **autenticação/autorização** .

3. Clique em **Azure Active Directory** na seção **provedores de autenticação** .

4. Defina o **modo de gerenciamento** como **avançado**.

5. Nas **URLs de redirecionamento externo permitidas**, insira `appname://easyauth.callback`.  O _AppName_ nesta cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir a especificação de URL normal para um protocolo (Use somente letras e números e comece com uma letra).  Você deve anotar a cadeia de caracteres que escolher, pois será necessário ajustar o código do aplicativo móvel com o esquema de URL em vários locais.

6. Clique em **OK**.

7. Clique em **Guardar**.

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Xcode, pressione **executar** para iniciar o aplicativo. Uma exceção é gerada porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela *TodoItem* agora requer autenticação.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
**Objective-C**:

1. No seu Mac, abra *QSTodoListViewController. m* no Xcode e adicione o seguinte método:

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

    Altere o *Google* para *MicrosoftAccount*, *Twitter*, *Facebook*ou *windowsazureactivedirectory* se você não estiver usando o Google como seu provedor de identidade. Se usar o Facebook, deve [domínios de Facebook da lista de permissões][1] na sua aplicação.

    Substitua o **urlScheme** por um nome exclusivo para seu aplicativo.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL especificado no campo **URLs de redirecionamento externas permitidas** no portal do Azure. O urlScheme é usado pelo retorno de chamada de autenticação para voltar ao aplicativo após a conclusão da solicitação de autenticação.

2. Substitua `[self refresh]` em `viewDidLoad` em *QSTodoListViewController. m* pelo seguinte código:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Abra o arquivo `QSAppDelegate.h` e adicione o seguinte código:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Abra o arquivo `QSAppDelegate.m` e adicione o seguinte código:

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

   Adicione este código diretamente antes que a linha seja lida `#pragma mark - Core Data stack`.  Substitua o _AppName_ pelo valor urlScheme que você usou na etapa 1.

5. Abra o arquivo `AppName-Info.plist` (substituindo AppName pelo nome do seu aplicativo) e adicione o seguinte código:

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

    Esse código deve ser colocado dentro do elemento `<dict>`.  Substitua a cadeia de caracteres _AppName_ (dentro da matriz para **CFBundleURLSchemes**) pelo nome do aplicativo que você escolheu na etapa 1.  Você também pode fazer essas alterações no editor do plist – clique no arquivo `AppName-Info.plist` no XCode para abrir o editor do plist.

    Substitua a cadeia de caracteres `com.microsoft.azure.zumo` para **CFBundleURLName** pelo seu identificador Apple Bundle.

6. Pressione *executar* para iniciar o aplicativo e, em seguida, faça logon. Quando você estiver conectado, você deverá ser capaz de exibir a lista de tarefas pendentes e fazer atualizações.

**Swift**:

1. No seu Mac, abra *ToDoTableViewController. Swift* no Xcode e adicione o seguinte método:

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

    Altere o *Google* para *MicrosoftAccount*, *Twitter*, *Facebook*ou *windowsazureactivedirectory* se você não estiver usando o Google como seu provedor de identidade. Se usar o Facebook, deve [domínios de Facebook da lista de permissões][1] na sua aplicação.

    Substitua o **urlScheme** por um nome exclusivo para seu aplicativo.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL especificado no campo **URLs de redirecionamento externas permitidas** no portal do Azure. O urlScheme é usado pelo retorno de chamada de autenticação para voltar ao aplicativo após a conclusão da solicitação de autenticação.

2. Remova as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` no final de `viewDidLoad()` em *ToDoTableViewController. Swift*. Adicione uma chamada para `loginAndGetData()` em seu lugar:

    ```swift
    loginAndGetData()
    ```

3. Abra o arquivo `AppDelegate.swift` e adicione a seguinte linha à classe `AppDelegate`:

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

    Substitua o _AppName_ pelo valor urlScheme que você usou na etapa 1.

4. Abra o arquivo `AppName-Info.plist` (substituindo AppName pelo nome do seu aplicativo) e adicione o seguinte código:

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

    Esse código deve ser colocado dentro do elemento `<dict>`.  Substitua a cadeia de caracteres _AppName_ (dentro da matriz para **CFBundleURLSchemes**) pelo nome do aplicativo que você escolheu na etapa 1.  Você também pode fazer essas alterações no editor do plist – clique no arquivo `AppName-Info.plist` no XCode para abrir o editor do plist.

    Substitua a cadeia de caracteres `com.microsoft.azure.zumo` para **CFBundleURLName** pelo seu identificador Apple Bundle.

5. Pressione *executar* para iniciar o aplicativo e, em seguida, faça logon. Quando você estiver conectado, você deverá ser capaz de exibir a lista de tarefas pendentes e fazer atualizações.

A autenticação do serviço de aplicativo usa as maçãs comunicação entre aplicativos.  Para obter mais detalhes sobre este assunto, consulte a [documentação da Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure portal]: https://portal.azure.com

[início rápido do iOS]: app-service-mobile-ios-get-started.md

