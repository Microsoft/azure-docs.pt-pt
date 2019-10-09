---
title: Como usar o plug-in Apache Cordova para aplicativos móveis do Azure
description: Como usar o plug-in Apache Cordova para aplicativos móveis do Azure
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3cf18e6da56b25e453d52dc58020961f672da27d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027443"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Como usar a biblioteca de cliente Apache Cordova para aplicativos móveis do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

## <a name="overview"></a>Descrição geral
Este guia ensina a executar cenários comuns usando o [Plug-in Apache Cordova para aplicativos móveis do Azure]. Se você for novo nos aplicativos móveis do Azure, primeiro conclua os [Início Rápido de aplicativos móveis do Azure] para criar um back-end, criar uma tabela e baixar um projeto de Apache Cordova predefinido. Neste guia, nos concentramos no plug-in de Apache Cordova do lado do cliente.

## <a name="supported-platforms"></a>Plataformas suportadas
Este SDK dá suporte a Apache Cordova v 6.0.0 e posterior em dispositivos iOS, Android e Windows.  O suporte à plataforma é o seguinte:

* API do Android 19-24 (KitKat até nougat).
* iOS versões 8,0 e posteriores.
* Windows Phone 8.1.
* Plataforma Universal do Windows.

## <a name="Setup"></a>Instalação e pré-requisitos
Este guia pressupõe que você criou um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema que as tabelas nesses tutoriais. Este guia também pressupõe que você adicionou o plug-in Apache Cordova ao seu código.  Se você não tiver feito isso, poderá adicionar o plug-in Apache Cordova ao seu projeto na linha de comando:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para obter mais informações sobre como criar [seu primeiro aplicativo Apache Cordova], consulte sua documentação.

## <a name="ionic"></a>Configurando um aplicativo Ionic v2

Para configurar corretamente um projeto do Ionic v2, primeiro crie um aplicativo básico e adicione o plug-in Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Adicione as seguintes linhas a `app.component.ts` para criar o objeto de cliente:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Agora você pode criar e executar o projeto no navegador:

```
ionic platform add browser
ionic run browser
```

O plug-in Cordova dos aplicativos móveis do Azure dá suporte a aplicativos Ionic v1 e v2.  Somente os aplicativos Ionic v2 exigem a declaração adicional para o objeto `WindowsAzure`.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Como: Autenticar utilizadores
Azure App serviço dá suporte à autenticação e à autorização de usuários de aplicativos usando vários provedores de identidade externos: Facebook, Google, conta da Microsoft e Twitter. Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente a usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts de servidor. Para obter mais informações, consulte o tutorial introdução [Introdução à autenticação] .

Ao usar a autenticação em um aplicativo Apache Cordova, os seguintes plug-ins do Cordova devem estar disponíveis:

* [Cordova-plugin-dispositivo]
* [Cordova-plugin-inappbrowser]

Há suporte para dois fluxos de autenticação: um fluxo de servidor e um fluxo de cliente.  O fluxo do servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da Web do provedor. O fluxo do cliente permite uma integração mais profunda com recursos específicos do dispositivo, como logon único, pois depende de SDKs específicos do dispositivo específicos do provedor.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Como: Configure seu serviço de aplicativo móvel para URLs de redirecionamento externas.
Vários tipos de aplicativos Apache Cordova usam um recurso de auto-retorno para lidar com fluxos de interface do usuário OAuth.  Os fluxos de interface do usuário do OAuth no localhost causam problemas, pois o serviço de autenticação só sabe como utilizar seu serviço por padrão.  Exemplos de fluxos de interface do usuário OAuth problemáticos incluem:

* O emulador do Ripple.
* Recarregamento ao vivo com Ionic.
* Executando o back-end móvel localmente
* Executar o back-end móvel em um serviço de Azure App diferente daquele que fornece autenticação.

Siga estas instruções para adicionar as configurações locais à configuração:

1. Inicie sessão no [Azure portal]
2. Selecione **todos os recursos** ou **serviços de aplicativos** e clique no nome do seu aplicativo móvel.
3. Clique em **ferramentas**
4. Clique em **Gerenciador de recursos** no menu observar e clique em **ir**.  Uma nova janela ou guia é aberta.
5. Expanda os nós **config**, **authsettings** para seu site no painel de navegação esquerdo.
6. Clique em **Editar**
7. Procure o elemento "allowedExternalRedirectUrls".  Pode ser definido como nulo ou uma matriz de valores.  Altere o valor para o seguinte valor:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua as URLs pelas URLs do seu serviço.  Os exemplos incluem `http://localhost:3000` (para o serviço de exemplo do node. js) ou `http://localhost:4400` (para o serviço do ripple).  No entanto, essas URLs são exemplos – sua situação, incluindo os serviços mencionados nos exemplos, pode ser diferente.
8. Clique no botão de **leitura/gravação** no canto superior direito da tela.
9. Clique no botão verde **Put** .

As configurações são salvas neste ponto.  Não feche a janela do navegador até que as configurações tenham terminado de salvar.
Além disso, adicione essas URLs de loopback às configurações de CORS para seu serviço de aplicativo:

1. Inicie sessão no [Azure portal]
2. Selecione **todos os recursos** ou **serviços de aplicativos** e clique no nome do seu aplicativo móvel.
3. A folha configurações é aberta automaticamente.  Se não estiver, clique em **todas as configurações**.
4. Clique em **CORS** no menu API.
5. Insira a URL que você deseja adicionar na caixa fornecida e pressione Enter.
6. Insira URLs adicionais conforme necessário.
7. Clique em **Guardar** para guardar as definições.

Leva aproximadamente 10-15 segundos para que as novas configurações entrem em vigor.

## <a name="register-for-push"></a>Como: Registrar-se para notificações por push
Instale o [PhoneGap-plugin-Push] para lidar com notificações por push.  Esse plug-in pode ser facilmente adicionado usando o comando `cordova plugin add` na linha de comando ou por meio do instalador de plug-in git no Visual Studio.  O código a seguir em seu aplicativo Apache Cordova registra seu dispositivo para notificações por push:

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

Use o SDK dos hubs de notificação para enviar notificações por push do servidor.  Nunca envie notificações por push diretamente de clientes. Isso pode ser usado para disparar um ataque de negação de serviço contra os hubs de notificação ou o PNS.  O PNS poderia proibir seu tráfego como resultado de tais ataques.

## <a name="more-information"></a>Mais informações

Você pode encontrar detalhes detalhados da API em nossa [documentação de API](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure portal]: https://portal.azure.com
[Início Rápido de aplicativos móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plug-in Apache Cordova para aplicativos móveis do Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[seu primeiro aplicativo Apache Cordova]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[PhoneGap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[Cordova-plugin-dispositivo]: https://www.npmjs.com/package/cordova-plugin-device
[Cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
