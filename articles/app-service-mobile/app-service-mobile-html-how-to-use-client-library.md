---
title: Usar o SDK do JavaScript
description: Como usar o v para aplicativos móveis do Azure
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 0f70523b5b27a4f3db86bacf39469e233e43f807
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668836"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Como usar a biblioteca de cliente JavaScript para aplicativos móveis do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este guia ensina a executar cenários comuns usando o SDK do [SDK do JavaScript para aplicativos móveis do Azure]. Se você for novo nos aplicativos móveis do Azure, primeiro conclua os [Início Rápido de aplicativos móveis do Azure] para criar um back-end e criar uma tabela. Neste guia, nos concentramos em usar o back-end móvel em aplicativos Web HTML/JavaScript.

## <a name="supported-platforms"></a>Plataformas suportadas
Limitamos o suporte do navegador às versões atual e última dos principais navegadores: Google Chrome, Microsoft Edge, Microsoft Internet Explorer e Mozilla Firefox.  Esperamos que o SDK funcione com qualquer navegador relativamente moderno.

O pacote é distribuído como um módulo JavaScript universal e, portanto, dá suporte aos formatos Globals, AMD e CommonJS.

## <a name="Setup"></a>Instalação e pré-requisitos
Este guia pressupõe que você criou um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema que as tabelas nesses tutoriais.

A instalação do SDK do JavaScript de aplicativos móveis do Azure pode ser feita por meio do comando `npm`:

```
npm install azure-mobile-apps-client --save
```

A biblioteca também pode ser usada como um módulo ES2015, em ambientes CommonJS, como Browserify e webpack, e como uma biblioteca AMD.  Por exemplo:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Você também pode usar uma versão criada previamente do SDK baixando diretamente de nossa CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Como autenticar usuários
Azure App serviço dá suporte à autenticação e à autorização de usuários de aplicativos usando vários provedores de identidade externos: Facebook, Google, conta da Microsoft e Twitter. Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente a usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts de servidor. Para obter mais informações, consulte o tutorial introdução [Introdução à autenticação] .

Há suporte para dois fluxos de autenticação: um fluxo de servidor e um fluxo de cliente.  O fluxo do servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da Web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, como logon único, pois depende de SDKs específicos do provedor.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Como configurar seu serviço de aplicativo móvel para URLs de redirecionamento externo.
Vários tipos de aplicativos JavaScript usam um recurso de auto-retorno para lidar com fluxos de interface do usuário OAuth.  Esses recursos incluem:

* Executando o serviço localmente
* Usando a recarga ao vivo com a estrutura Ionic
* Redirecionando para o serviço de aplicativo para autenticação.

Executar localmente pode causar problemas porque, por padrão, a autenticação do serviço de aplicativo é configurada apenas para permitir o acesso do back-end do aplicativo móvel. Use as etapas a seguir para alterar as configurações do serviço de aplicativo para habilitar a autenticação ao executar o servidor localmente:

1. Inicie sessão no [Portal do Azure]
2. Navegue até o back-end do aplicativo móvel.
3. Selecione **Gerenciador de recursos** no menu **ferramentas de desenvolvimento** .
4. Clique em **ir** para abrir o Gerenciador de recursos para o back-end do aplicativo móvel em uma nova guia ou janela.
5. Expanda o nó **config** > **authsettings** para seu aplicativo.
6. Clique no botão **Editar** para habilitar a edição do recurso.
7. Localize o elemento **allowedExternalRedirectUrls** , que deve ser nulo. Adicione suas URLs em uma matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua as URLs na matriz pelas URLs do seu serviço, que neste exemplo é `http://localhost:3000` para o serviço de exemplo node. js local. Você também pode usar `http://localhost:4400` para o serviço do Ripple ou alguma outra URL, dependendo de como seu aplicativo está configurado.
8. Na parte superior da página, clique em **leitura/gravação**e, em seguida, clique em **colocar** para salvar as atualizações.

Você também precisa adicionar as mesmas URLs de loopback às configurações da lista branca do CORS:

1. Navegue de volta para a [portal do Azure].
2. Navegue até o back-end do aplicativo móvel.
3. Clique em **CORS** no menu **API** .
4. Insira cada URL na caixa de texto **origens permitidas** vazia.  Uma nova caixa de texto é criada.
5. Clique em **salvar**

Após as atualizações de back-end, você poderá usar as novas URLs de loopback em seu aplicativo.

<!-- URLs. -->
[Início Rápido de aplicativos móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portal do Azure]: https://portal.azure.com/
[SDK do JavaScript para aplicativos móveis do Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
