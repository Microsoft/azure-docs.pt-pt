---
title: Como utilizar o plug-in do Apache Cordova para aplicações móveis do Azure
description: Como utilizar o plug-in do Apache Cordova para aplicações móveis do Azure
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 3c22aab20a9260bfd21869f0b327211e2f3d8894
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004216"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Como utilizar a biblioteca de clientes do Apache Cordova para aplicações móveis do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina-lhe executar tarefas comuns com a versão mais recente [Plug-in do Apache Cordova para aplicações móveis do Azure]. Se estiver familiarizado com aplicações móveis do Azure, primeiro conclua [Início rápido de aplicações móveis do Azure] para criar um back-end, criar uma tabela e transfira um projeto do Apache Cordova criado previamente. Neste guia, vamos nos concentrar no plug-in do lado do cliente Apache Cordova.

## <a name="supported-platforms"></a>Plataformas suportadas
Este SDK suporta v6.0.0 Apache Cordova e mais tarde no iOS, Android e Windows dispositivos.  O suporte de plataforma é o seguinte:

* Android API 24 de 19 (KitKat por meio de Nougat).
* iOS versões 8.0 e posteriores.
* Windows Phone 8.1.
* Plataforma universal do Windows.

## <a name="Setup"></a>Configuração e pré-requisitos
Este guia assume que criou um back-end com uma tabela. Este guia assume que a tabela tem o mesmo esquema que as tabelas nesses tutoriais. Este guia também pressupõe que adicionou o plug-in do Apache Cordova para o seu código.  Se não tiver feito isso, pode adicionar o plug-in do Cordova no Apache ao seu projeto na linha de comando:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para obter mais informações sobre a criação [sua primeira aplicação Apache Cordova], consulte sua documentação.

## <a name="ionic"></a>Como configurar uma aplicação de Ionic v2

Para configurar corretamente um projeto de Ionic v2, primeiro criar uma aplicação básica e adicione o plug-in Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Adicione as seguintes linhas para `app.component.ts` para criar o objeto de cliente:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Agora pode compilar e executar o projeto no browser:

```
ionic platform add browser
ionic run browser
```

O plug-in do Cordova de aplicações do Azure Mobile oferece suporte a ambas as aplicações v1 e v2 Ionic.  Apenas as aplicações de Ionic v2 requerem a declaração adicional para o `WindowsAzure` objeto.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Como: Autenticar utilizadores
Serviço de aplicações do Azure oferece suporte a autenticar e autorizar utilizadores da aplicação com vários fornecedores de identidade externo: Facebook, Google, conta Microsoft e Twitter. Pode definir permissões em tabelas para restringir o acesso para operações específicas para apenas os utilizadores autenticados. Também pode utilizar a identidade de usuários autenticados para implementar regras de autorização em scripts de servidor. Para obter mais informações, consulte a [introdução à autenticação] tutorial.

Ao utilizar a autenticação numa aplicação Apache Cordova, os plug-ins do Cordova seguintes têm de estar disponíveis:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Dois fluxos de autenticação são suportados: um fluxo de servidor e um fluxo de cliente.  O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende de interface de autenticação de web do fornecedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, tais como início de sessão único, pois depende de SDKs de dispositivo específicos de específica do fornecedor.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Como: Configure o serviço de aplicações móveis para os URLs de redirecionamento externo.
Vários tipos de aplicativos do Apache Cordova utilizam uma capacidade de loopback para lidar com fluxos de interface do Usuário do OAuth.  Fluxos de interface do Usuário de OAuth no localhost causam problemas, uma vez que o serviço de autenticação só sabe como utilizar o seu serviço por predefinição.  Exemplos de fluxos de interface do Usuário do OAuth problemáticos incluem:

* O emulador Ripple.
* Live recarregar com Ionic.
* Executar localmente o back-end móvel
* Num serviço de aplicações do Azure diferente do que a autenticação de fornecer um a executar o back-end móvel.

Siga estas instruções para adicionar as suas definições locais para a configuração:

1. Inicie sessão no [Portal do Azure]
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome da aplicação móvel.
3. Clique em **ferramentas**
4. Clique em **Explorador de recursos** no OBSERVE menu, em seguida, clique em **vá**.  É aberta uma nova janela ou separador.
5. Expanda a **config**, **authsettings** nós para o seu site no painel de navegação esquerdo.
6. Clique em **editar**
7. Procure o elemento de "allowedExternalRedirectUrls".  Ele pode ser definido como nulo ou uma matriz de valores.  Altere o valor para o seguinte valor:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua os URLs com os URLs do seu serviço.  Os exemplos incluem `http://localhost:3000` (para o serviço de exemplo de node. js), ou `http://localhost:4400` (para o serviço de cascata).  No entanto, estes URLs são exemplos - sua situação, incluindo os serviços mencionado nos exemplos, podem ser diferentes.
8. Clique nas **leitura/escrita** botão no canto superior direito da tela.
9. Clique no verde **colocar** botão.

As definições são guardadas neste momento.  Não feche a janela do browser até que as definições sejam concluídas ao guardar.
Adicione também estes URLs de loopback para as definições de CORS para o serviço de aplicações:

1. Inicie sessão no [Portal do Azure]
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome da aplicação móvel.
3. O painel de definições abre-se automaticamente.  Se não, clicar **todas as definições**.
4. Clique em **CORS** no menu de API.
5. Introduza o URL que pretende adicionar na caixa fornecida e prima Enter.
6. Introduza os URLs adicionais conforme necessário.
7. Clique em **Guardar** para guardar as definições.

Demora aproximadamente 10 a 15 segundos para as novas definições surtam efeito.

## <a name="register-for-push"></a>Como: Registre-se para notificações push
Instalar o [phonegap-Plug-in-push] para processar as notificações push.  Este plug-in pode ser facilmente adicionado usando o `cordova plugin add` comando na linha de comandos, ou através do instalador de plug-in do Git no Visual Studio.  O código a seguir na sua aplicação Apache Cordova regista o dispositivo para as notificações push:

```javascript
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Utilize o SDK dos Notification Hubs para enviar notificações push a partir do servidor.  Nunca envie notificações push diretamente a partir de clientes. Se o fizer, pode ser utilizado para acionar um ataque de negação de serviço contra os Hubs de notificação ou o PNS.  O PNS poderia banir o tráfego como resultado desses ataques.

## <a name="more-information"></a>Mais informações

Pode encontrar detalhes de API detalhadas em nossa [documentação da API](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com
[Início rápido de aplicações móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plug-in do Apache Cordova para aplicações móveis do Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[sua primeira aplicação Apache Cordova]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-Plug-in-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
