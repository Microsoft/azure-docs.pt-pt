---
title: Adicionar autenticação em Apache Cordova
description: Saiba como usar aplicações móveis no Serviço de Aplicações Azure para autenticar os utilizadores da sua aplicação Apache Cordova com fornecedores de identidade como Google, Facebook, Twitter e Microsoft.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3714ce2a8098608851991115aa82afdc00d08a47
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459393"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Adicione a autenticação à sua app Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumo
Neste tutorial, adiciona-se autenticação ao projeto de arranque rápido todo-lista no Apache Cordova utilizando um fornecedor de identidade suportado. Este tutorial é baseado no Get started com o tutorial de [Começar com aplicativos móveis] que você deve completar primeiro.

## <a name="register"></a>Registe a sua aplicação para autenticação e configure o Serviço de Aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Restringir permissões a utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, pode verificar se o acesso anónimo ao seu backend foi desativado. No Visual Studio:

* Abra o projeto que criou quando completou o tutorial [Começar com aplicativos móveis]
* Execute a sua aplicação no **Emulator Android**do Google .
* Verifique se uma falha de ligação inesperada é mostrada após o início da aplicação.

Em seguida, atualize a app para autenticar os utilizadores antes de solicitar recursos do backend da Aplicação Móvel.

## <a name="add-authentication"></a>Adicionar autenticação à app
1. Abra o seu projeto no **Visual Studio**e abra o ficheiro `www/index.html` para edição.
2. Localize a meta de `Content-Security-Policy` na secção da cabeça.  Adicione o anfitrião oAuth à lista de fontes permitidas.

   | Fornecedor | Nome do provedor SDK | Hospedeiro OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | google | https://accounts.google.com |
   | Microsoft | conta microsoft | https://login.live.com |
   | Twitter | twitter | https://api.twitter.com |

    Um exemplo de Política de Segurança de Conteúdo (implementado para o Diretório Ativo Azure) é o seguinte:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Substitua `https://login.microsoftonline.com` pelo hospedeiro OAuth da tabela anterior.  Para obter mais informações sobre a meta-tag de política de segurança do conteúdo, consulte a [Documentação sobre segurança de conteúdo]sobre a política de segurança do conteúdo .

    Alguns fornecedores de autenticação não requerem alterações na Política de Segurança do Conteúdo quando utilizados em dispositivos móveis apropriados.  Por exemplo, não são necessárias alterações na política de segurança de conteúdo quando se utiliza a autenticação do Google num dispositivo Android.

3. Abra o ficheiro `www/js/index.js` para edição, localize o método `onDeviceReady()` e, sob o código de criação do cliente, adicione o seguinte código:

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

    Este código substitui o código existente que cria a referência da tabela e atualiza a UI.

    O método de login() inicia a autenticação com o fornecedor. O método de login é uma função assinizada que devolve uma Promessa JavaScript.  O resto da inicialização é colocado dentro da resposta da promessa para que não seja executada até que o método de login() complete.

4. No código que acabou de adicionar, substitua `SDK_Provider_Name` pelo nome do seu fornecedor de login. Por exemplo, para o Azure Ative Directory, utilize `client.login('aad')`.
5. Gereno seu projeto.  Quando o projeto terminar de inicializar, a sua aplicação mostra a página de login DaOuth para o fornecedor de autenticação escolhido.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais [Sobre a Autenticação] com o Serviço de Aplicações Azure.
* Continue o tutorial adicionando [Notificações Push] à sua aplicação Apache Cordova.

Saiba como utilizar os SDKs.

* [SDK do Apache Cordova]
* [SDK do Servidor ASP.NET]
* [SDK do Servidor Node.js]

<!-- URLs. -->
[Começar com aplicativos móveis]: app-service-mobile-cordova-get-started.md
[Documentação sobre segurança de conteúdo]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notificações Push]: app-service-mobile-cordova-get-started-push.md
[Sobre a Autenticação]: app-service-mobile-auth.md
[SDK do Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do Servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do Servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
