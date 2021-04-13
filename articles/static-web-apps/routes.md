---
title: Rotas em Azure Static Web Apps
description: Saiba mais sobre as regras de encaminhamento back-end e como garantir rotas com papéis.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 0ddecff0162f8bd405c9f5fe6d3fdc20c6bc24aa
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313645"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Rotas em Azure Static Web Apps Preview

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

O encaminhamento em Azure Static Web Apps define regras de encaminhamento de back-end e comportamento de autorização tanto para conteúdo estático como APIs<sup>1</sup>. As regras são definidas como um conjunto de regras no _staticwebapp.config.jsarquivado._

- O _staticwebapp.config.jsem_ ficheiro deve existir na raiz da pasta de artefactos de construção da app.
- As regras são executadas na ordem tal como aparecem na `routes` matriz.
- A avaliação de regras para no primeiro jogo. As regras de encaminhamento não estão acorrentadas.
- As funções são definidas na _staticwebapp.config.jsem_ ficheiros e os utilizadores estão associados a funções através de [convites](authentication-authorization.md).
- Tens controlo total sobre os nomes dos papéis.

O tema do encaminhamento sobrepõe-se significativamente aos conceitos de autenticação e autorização. Certifique-se de que lê o guia [de autenticação e autorização](authentication-authorization.md) juntamente com este artigo.

Consulte o [ficheiro de rota de exemplo](#example-route-file) para obter mais detalhes.

## <a name="location"></a>Localização

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

O _staticwebapp.config.jsem_ ficheiro deve existir na raiz da pasta de artefactos de construção da app. Se a sua aplicação web inclui um passo de construção que copia ficheiros construídos de uma pasta específica para a sua pasta de artefactos de construção, então o _staticwebapp.config.jsno_ ficheiro precisa de existir nessa pasta específica.

A tabela que se segue lista a localização adequada para colocar o seu _staticwebapp.config.jsem_ arquivo para uma série de quadros e bibliotecas frontais.

| Quadro / biblioteca | Localização  |
| ------------------- | --------- |
| Angular             | _ativos_  |
| React               | _público_  |
| Svelte              | _público_  |
| Vue                 | _público_  |
| Blazor              | _wwwroot_ |

A tabela acima é apenas representativa de alguns quadros e bibliotecas compatíveis com Azure Static Web Apps. Consulte as [estruturas frontais e bibliotecas de configuração](./front-end-frameworks.md) para obter mais informações.

## <a name="defining-routes"></a>Definição de rotas

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

As rotas são definidas no _staticwebapp.config.jsarquivado_ como uma série de regras de rota na `routes` propriedade. Cada regra é composta por um padrão de rota, juntamente com uma ou mais das propriedades de regras opcionais. Consulte o [ficheiro de rota de exemplo](#example-route-file) para obter exemplos de utilização.

| Propriedade de regra  | Necessário | Valor predefinido | Comentário                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| -------------- | -------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`        | Yes      | n/a           | O padrão de rota solicitado pelo chamador.<ul><li>[Os wildcards](#wildcards) são suportados no final dos caminhos de rota. Por exemplo, a rota _\* admin/corresponde_ a qualquer rota sob o caminho _administrativo._<li>O ficheiro predefinido de uma rota é _index.html_.</ul>                                                                                                                                                                                                                                               |
| `serve`        | No       | n/a           | Define o ficheiro ou caminho devolvido do pedido. O caminho e o nome do ficheiro podem ser diferentes do caminho solicitado. Se um `serve` valor não for definido, então o caminho solicitado é usado. Os parâmetros de consulta não são suportados; `serve` os valores devem apontar para ficheiros reais.                                                                                                                                                                                                                 |
| `allowedRoles` | No       | anónimo     | Uma série de nomes de papéis. <ul><li>Os caracteres válidos `a-z` `A-Z` incluem, , e `0-9` `_` .<li>O papel incorporado `anonymous` aplica-se a todos os utilizadores não autenticados.<li>A função incorporada `authenticated` aplica-se a qualquer utilizador com sessão iniciada.<li>Os utilizadores devem pertencer a pelo menos uma função.<li>As funções são correspondidas numa base _de OR._ Se um utilizador estiver em alguma das funções listadas, então o acesso é concedido.<li>Os utilizadores individuais estão associados a funções através de [convites.](authentication-authorization.md)</ul> |
| `statusCode`   | No       | 200           | A resposta [do código de estado HTTP](https://wikipedia.org/wiki/List_of_HTTP_status_codes) para o pedido.                                                                                                                                                                                                                                                                                                                                                                                        |

## <a name="securing-routes-with-roles"></a>Assegurar rotas com funções

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

As rotas são asseguradas adicionando um ou mais nomes de papéis no conjunto de uma `allowedRoles` regra. Consulte o [ficheiro de rota de exemplo](#example-route-file) para obter exemplos de utilização.

Por predefinição, todos os utilizadores pertencem à `anonymous` função incorporada, e todos os utilizadores com login são membros do `authenticated` papel. Por exemplo, para restringir uma rota apenas para utilizadores autenticados, adicione a função incorporada `authenticated` à `allowedRoles` matriz.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Pode criar novos papéis conforme necessário na `allowedRoles` matriz. Para restringir uma rota apenas para administradores, você poderia definir um `administrator` papel na `allowedRoles` matriz.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Tem total controlo sobre os nomes das funções; não há nenhuma lista à qual os seus papéis devem aderir.
- Os utilizadores individuais estão associados a funções através de [convites.](authentication-authorization.md)

## <a name="wildcards"></a>Carateres universais

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

As regras wildcard correspondem a todos os pedidos sob um determinado padrão de rota. Se definir um `serve` valor na sua regra, o ficheiro ou caminho nomeado é servido como resposta.

Por exemplo, para implementar rotas para uma aplicação de calendário, você pode mapear todos os URLs que se enquadram na rota do _calendário_ para servir um único ficheiro.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

O ficheiro _calendar.html_ pode então utilizar o encaminhamento do lado do cliente para servir uma visão diferente para variações de URL como , `/calendar/january/1` e `/calendar/2020` `/calendar/overview` .

Também pode garantir rotas com wildcards. No exemplo seguinte, qualquer ficheiro solicitado no âmbito da via _de administração_ requer um utilizador autenticado que seja membro da função de _administrador._

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> Os pedidos de ficheiros referenciados por um ficheiro servido são apenas avaliados para verificações de autenticação. Por exemplo, os pedidos a um ficheiro CSS sob um caminho wildcard servem o ficheiro CSS, e não o que é definido como `serve` o ficheiro. O ficheiro CSS é servido desde que o utilizador cumpra os requisitos de autenticação exigidos.

## <a name="fallback-routes"></a>Rotas de recuo

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

As aplicações de página única, quer estejam a usar quadros javaScript frontais ou bibliotecas ou plataformas WebAssembly como o Blazor, dependem frequentemente do encaminhamento do lado do cliente para a navegação de aplicações web. Estas regras de encaminhamento do lado do cliente atualizam a localização da janela do navegador sem fazer pedidos de volta ao servidor. Se atualizar a página ou navegar diretamente para locais gerados pelas regras de encaminhamento do lado do cliente, é necessária uma rota de recuo do lado do servidor para servir a página HTML apropriada.

No seguinte exemplo é apresentada uma rota comum de recuo:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

A rota de retorno deve ser listada em último lugar nas suas regras de encaminhamento, uma vez que apanha todos os pedidos não apanhados por regras previamente definidas.

## <a name="redirects"></a>Redirecionamentos

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

Pode utilizar códigos de estado [301](https://en.wikipedia.org/wiki/HTTP_301) e [302](https://en.wikipedia.org/wiki/HTTP_302) HTTP para redirecionar pedidos de uma rota para outra.

Por exemplo, a seguinte regra cria um redirecionamento 301 de _old-page.html_ para _new-page.html_.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Os redirecionamentos também funcionam com caminhos que não definem ficheiros distintos.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Páginas de erro personalizadas

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

Os utilizadores podem encontrar uma série de situações diferentes que podem resultar num erro. Utilizando a `platformErrorOverrides` matriz, pode fornecer uma experiência personalizada em resposta a estes erros. Consulte o ficheiro de rota de [exemplo](#example-route-file) para a colocação da matriz no _staticwebapp.config.jsem_ ficheiro.

> [!NOTE]
> Uma vez que um pedido chega ao nível de sobreposição da plataforma, as regras de rota não são executadas novamente.

A tabela a seguir enumera os erros da plataforma disponíveis:

| Tipo de erro                                 | Código de estado de HTTP | Descrição                                                                                                                                                                                                                                                                                      |
| ------------------------------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `NotFound`                                 | 404              | Não se encontra uma página no servidor.                                                                                                                                                                                                                                                               |
| `Unauthenticated`                          | 401              | O utilizador não está a iniciar sessão com um [fornecedor de autenticação](authentication-authorization.md).                                                                                                                                                                                                    |
| `Unauthorized_InsufficientUserInformation` | 401              | A conta do utilizador no fornecedor de autenticação não está configurada para expor os dados necessários. Este erro pode ocorrer em situações como quando a aplicação pede ao fornecedor de autenticação o endereço de e-mail do utilizador, mas o utilizador optou por restringir o acesso ao endereço de e-mail.                    |
| `Unauthorized_InvalidInvitationLink`       | 401              | Um convite expirou ou o utilizador seguiu um link de convite gerado para outro destinatário.                                                                                                                                                                                       |
| `Unauthorized_MissingRoles`                | 401              | O utilizador não é membro de uma função necessária.                                                                                                                                                                                                                                                     |
| `Unauthorized_TooManyUsers`                | 401              | O site atingiu o número máximo de utilizadores, e o servidor está a limitar mais adições. Este erro é exposto ao cliente porque não há limite para o número de convites que pode gerar, e [alguns](authentication-authorization.md) utilizadores podem nunca aceitar o seu convite. |
| `Unauthorized_Unknown`                     | 401              | Existe um problema desconhecido ao tentar autenticar o utilizador. Uma das causas deste erro pode ser o facto de o utilizador não ser reconhecido por não ter dado consentimento à aplicação.                                                                                                          |

## <a name="custom-mime-types"></a>Tipos de mímica personalizados

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

O `mimeTypes` objeto, listado ao mesmo nível da `routes` matriz, permite associar [tipos de MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types) a extensões de ficheiros.

```json
{
  "routes": [],
  "mimeTypes": {
    "custom": "text/html"
  }
}
```

No exemplo acima, todos os ficheiros com a `.custom` extensão são servidos com o `text/html` tipo MIME.

As seguintes considerações são importantes porque trabalha com tipos de MIME:

- As teclas não podem ser nulas ou vazias, ou mais de 50 caracteres
- Valores não podem ser nulos ou vazios, ou mais de 1000 caracteres

> [!NOTE]
> As Aplicações Web Estáticas compreendem as aplicações Blazor e os tipos de MIME esperados para os ficheiros WASM e DLL, não precisa de adicionar mapeamentos para esses ficheiros.

## <a name="default-headers"></a>Cabeçalhos predefinidos

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

O `defaultHeaders` objeto, listado ao mesmo nível da `routes` matriz, permite-lhe adicionar, modificar ou remover [cabeçalhos de resposta](https://developer.mozilla.org/docs/Web/HTTP/Headers).

Fornecendo um valor para um cabeçalho adiciona ou modifica o cabeçalho. Fornecendo um valor vazio, remove o cabeçalho de ser servido ao cliente.

```json
{
  "routes": [],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'",
    "cache-control": "must-revalidate, max-age=6000",
    "x-dns-prefetch-control": ""
  }
}
```

No exemplo acima, é adicionado um novo `content-security-policy` cabeçalho, modifica `cache-control` o valor predefinido do servidor e o `x-dns-prefetch-control` cabeçalho é removido.

As seguintes considerações são importantes porque trabalha com cabeçalhos:

- As chaves não podem ser nulas ou vazias.
- Valores nulos ou vazios removam um cabeçalho do processamento.
- As teclas ou valores não podem exceder 8.000 caracteres.
- Os cabeçalhos definidos são servidos com todos os pedidos.
- Os cabeçalhos definidos em _staticwebapp.config.js_ aplicam-se apenas ao conteúdo estático. Pode personalizar cabeçalhos de resposta de um ponto final da API no código da função.

## <a name="example-route-file"></a>Arquivo de rota de exemplo

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada e melhor implementada no [ficheiro de configuração](./configuration.md#routes)de Aplicações Web Estáticas Azure .

O exemplo a seguir mostra como construir regras de rotas para conteúdo estático e APIs numa _staticwebapp.config.jsem_ arquivo. Algumas rotas utilizam a pasta do sistema [ _/.auth_](authentication-authorization.md) que acede aos pontos finais relacionados com a autenticação.

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "302",
      "serve": "/login"
    }
  ],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
    "custom": "text/html"
  }
}
```

Os exemplos a seguir descrevem o que acontece quando um pedido corresponde a uma regra.

| Pedidos para...                     | Resultado em...                                                                                                                                                                                                                                                                                                   |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _/perfil_                         | Os utilizadores autenticados são servidos o ficheiro _/perfil/index.html._ Utilizadores não autenticados redirecionados para _/login_.                                                                                                                                                                                                   |
| _/administração/relatórios_                   | Os utilizadores autenticados na função de administradores são _servidos_ o ficheiro _/administrador/reports/index.html._ Os utilizadores autenticados que não estão na função de administrador são _servidos_ um erro 401 <sup>2</sup>. Utilizadores não autenticados redirecionados para _/login_.                                                                       |
| _/api/administrador_                       | Os pedidos de utilizadores autenticados na função de administradores são _enviados_ para a API. Os utilizadores autenticados que não estão na função _de administrador_ e os utilizadores não autenticados são notificados de um erro de 401.                                                                                                                     |
| _/clientes/contoso_               | Os utilizadores autenticados que pertençam aos administradores ou _customers_contoso_ funções são _servidos_ o _ficheiro /clientes/contoso/index.html_ <sup>2</sup>. Os utilizadores autenticados que não estão nos administradores ou _customers_contoso_ funções são _servidos_ um erro de 401. Utilizadores não autenticados redirecionados para _/login_. |
| _/login_                           | Os utilizadores não autenticados são desafiados a autenticar com o GitHub.                                                                                                                                                                                                                                              |
| _/.auth/login/twitter_             | A autorização com o Twitter é desativada. O servidor responde com um erro 404.                                                                                                                                                                                                                                  |
| _/logout_                          | Os utilizadores são registados fora de qualquer fornecedor de autenticação.                                                                                                                                                                                                                                                           |
| _/calendário/2020/01_                | O navegador é servido o ficheiro _/calendar.html._                                                                                                                                                                                                                                                               |
| _/especiais_                        | O navegador é redirecionado para _/ofertas._                                                                                                                                                                                                                                                                         |
| _/pasta desconhecida_                  | O ficheiro _/custom-404.html_ é servido.                                                                                                                                                                                                                                                                         |
| Ficheiros com a `.custom` extensão | São servidos com o `text/html` tipo MIME                                                                                                                                                                                                                                                                      |

Todas as respostas incluem os `content-security-policy` cabeçalhos com um valor de `default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'` .

<sup>1</sup> As regras de rota para as funções de API [apenas suportam redirecionamentos](#redirects) e [asseguram rotas com funções.](#securing-routes-with-roles)

<sup>2</sup> Pode fornecer uma página de erro personalizada definindo uma `Unauthorized_MissingRoles` regra na `platformErrorOverrides` matriz.

## <a name="restrictions"></a>Restrições

- O _staticwebapp.config.jsno_ ficheiro não pode ser superior a 100 KB
- O _staticwebapp.config.jsno_ ficheiro suporta um máximo de 50 funções distintas

Consulte o [artigo Quotas](quotas.md) para restrições e limitações gerais.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Autenticação e autorização de instalação](authentication-authorization.md)
