---
title: Adicionar a autenticação no Apache Cordova com as aplicações móveis | Documentos da Microsoft
description: Saiba como utilizar aplicações móveis no App Service do Azure para autenticar os utilizadores da sua aplicação Apache Cordova, por meio de diversos fornecedores de identidade, incluindo Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 23b5967782cf237ed5af2b802aabbbf9c2f781e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62114215"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Adicionar autenticação à aplicação Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumo
Neste tutorial, vai adicionar autenticação ao projeto de início rápido todolist no Apache Cordova utilizando um fornecedor de identidade com suporte. Este tutorial baseia-se sobre o [introdução às Mobile Apps] tutorial, o que tem de concluir primeiro.

## <a name="register"></a>Registar a sua aplicação para autenticação e configurar o serviço de aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, pode verificar que o acesso anônimo ao seu back-end foi desabilitado. No Visual Studio:

* Abra o projeto que criou quando concluir o tutorial [introdução às Mobile Apps].
* Execute a sua aplicação **emulador do Google Android**.
* Certifique-se de que uma falha inesperada da ligação é apresentada depois da aplicação for iniciada.

Em seguida, atualize a aplicação para autenticar os utilizadores antes de solicitar recursos de back-end da aplicação móvel.

## <a name="add-authentication"></a>Adicionar autenticação à aplicação
1. Abra o projeto no **Visual Studio**, em seguida, abra o `www/index.html` arquivo para edição.
2. Localize o `Content-Security-Policy` marca meta na seção de cabeçalho.  Adicione o anfitrião de OAuth para a lista de origens permitidas.

   | Fornecedor | Nome do fornecedor de SDK | OAuth Host |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | twitter | https://api.twitter.com |

    Um exemplo de política de segurança de conteúdo (implementado para o Azure Active Directory) é o seguinte:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Substitua `https://login.microsoftonline.com` com o anfitrião de OAuth da tabela acima.  Para obter mais informações sobre a marca meta de política de segurança de conteúdo, consulte a [documentação de política de segurança de conteúdo].

    Alguns fornecedores de autenticação não necessitam de que política de segurança de conteúdo é alterado quando utilizado em dispositivos móveis apropriados.  Por exemplo, sem alterações de política de segurança de conteúdo são necessárias ao utilizar a autenticação do Google num dispositivo Android.

3. Abra o `www/js/index.js` de ficheiros para a edição, localize o `onDeviceReady()` método, e abaixo da criação de cliente código, adicione o seguinte código:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Este código substitui o código existente, que cria a referência de tabela e atualiza a interface do Usuário.

    O método login() começa a autenticação com o fornecedor. O método login() é uma função de async que retorna uma promessa de JavaScript.  O resto da inicialização é colocado dentro da resposta de promessa, para que ele não é executado até que o método login() seja concluída.

4. No código que acabou de adicionar, substituir `SDK_Provider_Name` com o nome do seu fornecedor de início de sessão. Por exemplo, para o Azure Active Directory, utilize `client.login('aad')`.
5. Execute o seu projeto.  Quando o projeto concluiu a inicialização, o seu aplicativo mostra a página de início de sessão OAuth para o fornecedor de autenticação escolhido.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais [sobre a autenticação] com o serviço de aplicações do Azure.
* Continue o tutorial adicionando [notificações Push] à sua aplicação Apache Cordova.

Saiba como utilizar os SDKs.

* [SDK do Apache Cordova]
* [SDK do Servidor ASP.NET]
* [SDK do Servidor Node.js]

<!-- URLs. -->
[Introdução às Mobile Apps]: app-service-mobile-cordova-get-started.md
[Documentação de política de segurança de conteúdo]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notificações Push]: app-service-mobile-cordova-get-started-push.md
[Sobre a autenticação]: app-service-mobile-auth.md
[SDK do Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do Servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do Servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
