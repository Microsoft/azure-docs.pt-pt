---
title: Como utilizar o SDK de JavaScript para aplicações móveis do Azure
description: Como uso v para aplicações móveis do Azure
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 16871bdc59d141334bc2c95f26929f270d7971cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106749"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Como utilizar a biblioteca de cliente JavaScript para aplicações móveis do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina-lhe executar tarefas comuns com a versão mais recente [SDK de JavaScript para aplicações móveis do Azure]. Se estiver familiarizado com aplicações móveis do Azure, primeiro conclua [Início rápido de aplicações móveis do Azure] para criar um back-end e criar uma tabela. Neste guia, vamos nos concentrar sobre como utilizar o back-end móvel em aplicativos Web HTML/JavaScript.

## <a name="supported-platforms"></a>Plataformas suportadas
Limitamos o suporte do browser para as versões atuais e o apelido dos navegadores principais:  Google Chrome, o Microsoft Edge, o Microsoft Internet Explorer e o Mozilla Firefox.  Esperamos que o SDK para funcionar com qualquer navegador moderno relativamente.

O pacote é distribuído como um módulo de JavaScript Universal, para que ele oferece suporte a globals, AMD, e CommonJS formatos.

## <a name="Setup"></a>Configuração e pré-requisitos
Este guia assume que criou um back-end com uma tabela. Este guia assume que a tabela tem o mesmo esquema que as tabelas nesses tutoriais.

Instalar o Azure Mobile Apps JavaScript SDK pode ser feito por meio do `npm` comando:

```
npm install azure-mobile-apps-client --save
```

A biblioteca também pode ser usada como um módulo de ES2015, em ambientes de CommonJS como Browserify e Webpack e como uma biblioteca AMD.  Por exemplo:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Também pode utilizar uma versão pré-criados do SDK ao transferir diretamente a partir de nossa CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Como: Autenticar utilizadores
Serviço de aplicações do Azure oferece suporte a autenticar e autorizar utilizadores da aplicação com vários fornecedores de identidade externo: Facebook, Google, conta Microsoft e Twitter. Pode definir permissões em tabelas para restringir o acesso para operações específicas para apenas os utilizadores autenticados. Também pode utilizar a identidade de usuários autenticados para implementar regras de autorização em scripts de servidor. Para obter mais informações, consulte a [introdução à autenticação] tutorial.

Dois fluxos de autenticação são suportados: um fluxo de servidor e um fluxo de cliente.  O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende de interface de autenticação de web do fornecedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, tais como início de sessão único, pois depende SDKs específicos de fornecedor.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Como: Configure o serviço de aplicações móveis para os URLs de redirecionamento externo.
Vários tipos de aplicativos JavaScript utilizam um recurso de loopback para processar fluxos de interface do Usuário do OAuth.  Estas funcionalidades incluem:

* Executar o seu serviço localmente
* Usando o recarregamento em direto com o Framework Ionic
* A redirecionar para o serviço de aplicações para autenticação.

A executar localmente pode causar problemas, porque, por predefinição, a autenticação do serviço de aplicações só está configurada para permitir o acesso de back-end da aplicação móvel. Utilize os seguintes passos para alterar as definições de serviço de aplicações para ativar a autenticação ao executar o servidor localmente:

1. Inicie sessão no [Portal do Azure]
2. Navegue para o back-end de aplicação móvel.
3. Selecione **Explorador de recursos** no **ferramentas de desenvolvimento** menu.
4. Clique em **ir** para abrir o Explorador de recursos para o back-end de aplicação móvel num novo separador ou janela.
5. Expanda a **config** > **authsettings** nó para a sua aplicação.
6. Clique nas **editar** botão para ativar a edição do recurso.
7. Encontrar o **allowedExternalRedirectUrls** elemento, que deve ser nulo. Adicione suas URLs numa matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua os URLs na matriz com os URLs do seu serviço, que, neste exemplo, é `http://localhost:3000` para o serviço de exemplo de node. js local. Também pode usar `http://localhost:4400` para o serviço de cascata ou outra URL, dependendo de como a sua aplicação está configurada.
8. Na parte superior da página, clique em **leitura/escrita**, em seguida, clique em **colocar** para guardar as atualizações.

Também terá de adicionar os mesmos URLs de loopback para as definições de lista de permissões CORS:

1. Navegue de volta para o [portal do Azure].
2. Navegue para o back-end de aplicação móvel.
3. Clique em **CORS** no **API** menu.
4. Introduza cada URL na vazio **origens permitidas** caixa de texto.  É criada uma nova caixa de texto.
5. Clique em **guardar**

Após o back-end de atualizações, será capaz de usar os novos URLs de loopback na sua aplicação.

<!-- URLs. -->
[Início rápido de aplicações móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portal do Azure]: https://portal.azure.com/
[SDK de JavaScript para aplicações móveis do Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
