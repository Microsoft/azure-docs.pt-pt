---
title: Adicionar autenticação em Apache Cordova com aplicativos móveis | Microsoft Docs
description: Saiba como usar aplicativos móveis no serviço de Azure App para autenticar usuários de seu aplicativo Apache Cordova por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d5e124c6f73285efdaef515deba5816093a27e98
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027580"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Adicionar autenticação ao seu aplicativo Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

## <a name="summary"></a>Resumo
Neste tutorial, você adicionará autenticação ao projeto de início rápido de ToDoList em Apache Cordova usando um provedor de identidade com suporte. Este tutorial se baseia no tutorial [introdução aos aplicativos móveis] , que você deve concluir primeiro.

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar o serviço de aplicativo
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, você pode verificar se o acesso anônimo ao seu back-end foi desabilitado. No Visual Studio:

* Abra o projeto que você criou quando concluiu o tutorial introdução [Introdução aos aplicativos móveis].
* Execute seu aplicativo no **Google Android Emulator**.
* Verifique se uma falha de conexão inesperada é mostrada depois que o aplicativo é iniciado.

Em seguida, atualize o aplicativo para autenticar os usuários antes de solicitar recursos do back-end do aplicativo móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
1. Abra o projeto no **Visual Studio**e abra o arquivo `www/index.html` para edição.
2. Localize a marca meta `Content-Security-Policy` na seção de cabeçalho.  Adicione o host OAuth à lista de fontes permitidas.

   | Fornecedor | Nome do provedor do SDK | Host OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | AAD | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | MicrosoftAccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Um exemplo de política de segurança de conteúdo (implementado para Azure Active Directory) é o seguinte:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Substitua `https://login.microsoftonline.com` pelo host OAuth da tabela anterior.  Para obter mais informações sobre a marca meta de política de segurança de conteúdo, consulte a [Documentação de conteúdo-segurança-política].

    Alguns provedores de autenticação não exigem alterações de política de segurança de conteúdo quando usados em dispositivos móveis apropriados.  Por exemplo, nenhuma alteração de política de segurança de conteúdo é necessária ao usar a autenticação do Google em um dispositivo Android.

3. Abra o arquivo `www/js/index.js` para edição, localize o método `onDeviceReady()` e, sob o código de criação do cliente, adicione o seguinte código:

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

    Esse código substitui o código existente que cria a referência de tabela e atualiza a interface do usuário.

    O método login () inicia a autenticação com o provedor. O método login () é uma função Async que retorna uma promessa de JavaScript.  O restante da inicialização é colocado dentro da resposta da promessa para que ela não seja executada até que o método de logon () seja concluído.

4. No código que você acabou de adicionar, substitua `SDK_Provider_Name` pelo nome do seu provedor de logon. Por exemplo, para Azure Active Directory, use `client.login('aad')`.
5. Execute seu projeto.  Quando o projeto terminar de inicializar, seu aplicativo mostrará a página de logon do OAuth para o provedor de autenticação escolhido.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais [sobre a autenticação] com o serviço de Azure app.
* Continue o tutorial adicionando [Notificações Push] ao seu aplicativo Apache Cordova.

Saiba como utilizar os SDKs.

* [SDK do Apache Cordova]
* [SDK do Servidor ASP.NET]
* [SDK do Servidor Node.js]

<!-- URLs. -->
[Introdução aos aplicativos móveis]: app-service-mobile-cordova-get-started.md
[Documentação de conteúdo-segurança-política]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notificações Push]: app-service-mobile-cordova-get-started-push.md
[Sobre a autenticação]: app-service-mobile-auth.md
[SDK do Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do Servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do Servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
