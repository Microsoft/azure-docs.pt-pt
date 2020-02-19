---
title: Como usar o Plugin Apache Cordova
description: Como usar o Plugin Apache Cordova para aplicações móveis Azure
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: cafeea8afe571fc81548833952eee72a695fed41
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459366"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Como usar a biblioteca cliente Apache Cordova para aplicações móveis Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Descrição geral
Este guia ensina-o a realizar cenários comuns utilizando o mais recente [Plugin Apache Cordova para aplicações móveis Azure]. Se você é novo em Aplicativos Móveis Azure, primeiro complete [Aplicativos móveis Azure Arranque rápido] para criar um backend, criar uma mesa e baixar um projeto Apache Cordova pré-construído. Neste guia, focamo-nos no Apache Cordova Plugin do lado do cliente.

## <a name="supported-platforms"></a>Plataformas suportadas
Este SDK suporta apache Cordova v6.0.0 e mais tarde em dispositivos iOS, Android e Windows.  O suporte da plataforma é o seguinte:

* Android API 19-24 (KitKat através de Nougat).
* versões iOS 8.0 e posteriormente.
* Windows Phone 8.1.
* Plataforma Universal Windows.

## <a name="Setup"></a>Configuração e pré-requisitos
Este guia assume que criou um backend com uma mesa. Este guia assume que a mesa tem o mesmo esquema que as mesas nesses tutoriais. Este guia também assume que adicionou o Plugin Apache Cordova ao seu código.  Se não o tiver feito, poderá adicionar o plugin Apache Cordova ao seu projeto na linha de comando:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para obter mais informações sobre a criação da [sua primeira app Apache Cordova]consulte a documentação.

## <a name="ionic"></a>Criação de uma aplicação Iónica V2

Para configurar corretamente um projeto Iónico v2, primeiro crie uma aplicação básica e adicione o plugin Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Adicione as seguintes linhas a `app.component.ts` para criar o objeto do cliente:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Agora pode construir e executar o projeto no navegador:

```
ionic platform add browser
ionic run browser
```

O plugin Azure Mobile Apps Cordova suporta aplicações Ionic v1 e v2.  Apenas as aplicações Iónicas v2 requerem a declaração adicional para o objeto `WindowsAzure`.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Como: Autenticar utilizadores
O Azure App Service suporta autenticar e autorizar utilizadores de aplicações utilizando vários fornecedores de identidade externa: Facebook, Google, Microsoft Account e Twitter. Pode definir permissões nas tabelas para restringir o acesso a operações específicas apenas a utilizadores autenticados. Também pode utilizar a identidade dos utilizadores autenticados para implementar regras de autorização em scripts de servidores. Para mais informações, consulte o Get iniciado com tutorial de [Começar com a autenticação]

Ao utilizar a autenticação numa aplicação Apache Cordova, devem estar disponíveis os seguintes plugins Cordova:

* [cordova-plugin-dispositivo]
* [cordova-plugin-inappbrowser]

São suportados dois fluxos de autenticação: um fluxo de servidor e um fluxo de cliente.  O fluxo do servidor proporciona a experiência de autenticação mais simples, uma vez que se baseia na interface de autenticação web do fornecedor. O fluxo do cliente permite uma integração mais profunda com capacidades específicas do dispositivo, tais como um único sinal, uma vez que se baseia em SDKs específicos do dispositivo específico do fornecedor.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Como configurar o seu Serviço de Aplicações Móveis para URLs de redirecionamento externo.
Vários tipos de aplicações Apache Cordova usam uma capacidade de backback para lidar com fluxos de UI OAuth.  Os fluxos de UI oAuth no local causam problemas, uma vez que o serviço de autenticação só sabe utilizar o seu serviço por defeito.  Exemplos de fluxos problemáticos da UI da OAuth incluem:

* O emulador ripple.
* Recarregar ao vivo com iónico.
* Executando o backend móvel localmente
* Executar o backend móvel num Serviço de Aplicações Azure diferente daquele que fornece a autenticação.

Siga estas instruções para adicionar as definições locais à configuração:

1. Inicie sessão no [Portal do Azure]
2. Selecione **Todos os recursos** ou Serviços de **Aplicações** e, em seguida, clique no nome da sua Aplicação Móvel.
3. Clique **em Ferramentas**
4. Clique no **explorador de recursos** no menu OBSERVE e, em seguida, clique em **Ir**.  Abre-se uma nova janela ou separador.
5. Expanda os nós **de config,** **authsettings** para o seu site na navegação à esquerda.
6. Clique em **Editar**
7. Procure o elemento "ExternalRedirectUrls permitido".  Pode ser definido como nulo ou uma série de valores.  Alterar o valor para o seguinte valor:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua os URLs com os URLs do seu serviço.  Exemplos incluem `http://localhost:3000` (para o serviço de amostra node.js), ou `http://localhost:4400` (para o serviço Ripple).  No entanto, estes URLs são exemplos - a sua situação, incluindo para os serviços mencionados nos exemplos, pode ser diferente.
8. Clique no botão **Ler/Escrever** no canto superior direito do ecrã.
9. Clique no botão **PUT** verde.

As definições são guardadas neste momento.  Não feche a janela do navegador até que as definições tenham terminado de poupar.
Adicione também estes URLs de loopback às definições CORS para o seu Serviço de Aplicações:

1. Inicie sessão no [Portal do Azure]
2. Selecione **Todos os recursos** ou Serviços de **Aplicações** e, em seguida, clique no nome da sua Aplicação Móvel.
3. A lâmina Definições abre-se automaticamente.  Se não o fizer, clique em **Todas as Definições**.
4. Clique em **CORS** no menu API.
5. Introduza o URL que pretende adicionar na caixa fornecida e prima Enter.
6. Introduza URLs adicionais conforme necessário.
7. Clique em **Guardar** para guardar as definições.

Demora aproximadamente 10-15 segundos para que as novas definições entrem em vigor.

## <a name="register-for-push"></a>Como: Registar-se para notificações push
Instale o [phonegap-plugin-push] para manusear notificações push.  Este plugin pode ser facilmente adicionado utilizando o comando `cordova plugin add` na linha de comando, ou através do instalador de plugin Git dentro do Estúdio Visual.  O seguinte código na sua aplicação Apache Cordova regista o seu dispositivo para notificações push:

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

Utilize o SDK dos Centros de Notificação para enviar notificações push do servidor.  Nunca envie notificações push diretamente dos clientes. Ao fazê-lo, poderia ser usado para desencadear um ataque de negação de serviço contra os Centros de Notificação ou o PNS.  O PNS pode proibir o seu tráfego como resultado de tais ataques.

## <a name="more-information"></a>Mais informações

Pode encontrar detalhes detalhados da API na nossa [documentação da API.](https://azure.github.io/azure-mobile-apps-js-client/)

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com
[Aplicativos móveis Azure Arranque rápido]: app-service-mobile-cordova-get-started.md
[Começar com a autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plugin Apache Cordova para aplicações móveis Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[sua primeira app Apache Cordova]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-dispositivo]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
