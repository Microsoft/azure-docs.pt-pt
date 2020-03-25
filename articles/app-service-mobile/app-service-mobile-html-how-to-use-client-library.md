---
title: Use o JavaScript SDK
description: Como usar v para aplicações móveis Azure
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 029b01f3aacc928ebdae0e8fe90871437afccea5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461526"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Como utilizar a biblioteca de clientes JavaScript para aplicações móveis Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Descrição geral
Este guia ensina-o a realizar cenários comuns utilizando o mais recente [JavaScript SDK para aplicações móveis Azure]. Se você é novo em Aplicativos Móveis Azure, primeiro complete [Aplicativos móveis Azure Arranque rápido] para criar um backend e criar uma tabela. Neste guia, focamo-nos em utilizar o backend móvel nas aplicações Web HTML/JavaScript.

## <a name="supported-platforms"></a>Plataformas suportadas
Limitamos o suporte ao navegador para as versões atuais e últimas dos principais navegadores: Google Chrome, Microsoft Edge, Microsoft Internet Explorer e Mozilla Firefox.  Esperamos que o SDK funcione com qualquer navegador relativamente moderno.

O pacote é distribuído como módulo JavaScript Universal, por isso suporta formatos globais, AMD e CommonJS.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Configuração e pré-requisitos
Este guia assume que criou um backend com uma mesa. Este guia assume que a mesa tem o mesmo esquema que as mesas nesses tutoriais.

A instalação das aplicações móveis Azure JavaScript SDK pode ser feita através do comando `npm`:

```
npm install azure-mobile-apps-client --save
```

A biblioteca também pode ser usada como um módulo ES2015, dentro de ambientes CommonJS como Browserify e Webpack e como uma biblioteca AMD.  Por exemplo:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Também pode utilizar uma versão pré-construída do SDK, baixando diretamente do nosso CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Como: Autenticar utilizadores
O Azure App Service suporta autenticar e autorizar utilizadores de aplicações utilizando vários fornecedores de identidade externa: Facebook, Google, Microsoft Account e Twitter. Pode definir permissões nas tabelas para restringir o acesso a operações específicas apenas a utilizadores autenticados. Também pode utilizar a identidade dos utilizadores autenticados para implementar regras de autorização em scripts de servidores. Para mais informações, consulte o Get iniciado com tutorial de [Começar com a autenticação]

São suportados dois fluxos de autenticação: um fluxo de servidor e um fluxo de cliente.  O fluxo do servidor proporciona a experiência de autenticação mais simples, uma vez que se baseia na interface de autenticação web do fornecedor. O fluxo do cliente permite uma integração mais profunda com capacidades específicas do dispositivo, tais como um único sign-on, uma vez que se baseia em SDKs específicos do fornecedor.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Como configurar o seu Serviço de Aplicações Móveis para URLs de redirecionamento externo.
Vários tipos de aplicações JavaScript usam uma capacidade de backback para lidar com fluxos ui oAuth.  Estas capacidades incluem:

* Executar o seu serviço localmente
* Utilização de Recarga Ao Vivo com o Quadro Iónico
* Redirecionamento para o Serviço de Aplicações para autenticação.

Correr localmente pode causar problemas porque, por padrão, a autenticação do Serviço de Aplicações só está configurada para permitir o acesso a partir do seu backend da Aplicação Móvel. Utilize os seguintes passos para alterar as definições do Serviço de Aplicações para permitir a autenticação ao executar o servidor localmente:

1. Inicie sessão no [Portal do Azure]
2. Navegue para o seu backend da Aplicação Móvel.
3. Selecione **Explorador de Recursos** no menu FERRAMENTAS DE **DESENVOLVIMENTO.**
4. Clique **em Ir** para abrir o explorador de recursos para o seu backend da Aplicação Móvel em um novo separador ou janela.
5. Expanda o nó de **authsettings** **de > config** para a sua aplicação.
6. Clique no botão **Editar** para permitir a edição do recurso.
7. Encontre o elemento **ExternalRedirectUrls permitido,** que deve ser nulo. Adicione os seus URLs numa matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua os URLs na matriz pelos URLs do seu serviço, que neste exemplo é `http://localhost:3000` para o serviço de amostra nó.js local. Também pode utilizar `http://localhost:4400` para o serviço Ripple ou qualquer outro URL, dependendo da configuração da sua aplicação.
8. No topo da página, clique em **Ler/Escrever**e, em seguida, clique em **PUT** para guardar as suas atualizações.

Também precisa de adicionar os mesmos URLs de loopback às definições da lista de permissões CORS:

1. Navegue de volta ao [Portal do Azure]
2. Navegue para o seu backend da Aplicação Móvel.
3. Clique em **CORS** no menu **API.**
4. Introduza cada URL na caixa de texto de **origem sintetizadora** vazia.  É criada uma nova caixa de texto.
5. Clique em **SAVE**

Após as atualizações de backend, poderá utilizar os novos URLs de backback na sua aplicação.

<!-- URLs. -->
[Aplicativos móveis Azure Arranque rápido]: app-service-mobile-cordova-get-started.md
[Começar com a autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portal do Azure]: https://portal.azure.com/
[JavaScript SDK para aplicações móveis Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
