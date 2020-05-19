---
title: Rotas em Aplicativos Web Azure Estáticas
description: Aprenda sobre as regras de encaminhamento de back-end e como garantir rotas com papéis.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 4a9639343827ebc5bb17a6d62d9b65d0b561e932
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597745"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Rotas na pré-visualização de aplicações da Web estática do Azure

O encaminhamento em Aplicações Web Estáticas Azure define regras de encaminhamento de back-end e comportamento de autorização tanto para conteúdo estático como para APIs. As regras são definidas como uma série de regras no ficheiro _routes.json._

- O ficheiro _routes.json_ deve existir na raiz da pasta de artefactos de construção da app.
- As regras são executadas na ordem como aparecem na `routes` matriz.
- A avaliação das regras para no primeiro jogo. As regras de encaminhamento não estão acorrentadas.
- As funções são definidas nos ficheiros _routes.json_ e os utilizadores estão associados a funções através [de convites](authentication-authorization.md).
- Tens controlo total sobre nomes de papéis.

O tema do encaminhamento sobrepõe-se significativamente aos conceitos de autenticação e autorização. Leia o guia de [autenticação e autorização](authentication-authorization.md) juntamente com este artigo.

## <a name="location"></a>Localização

O ficheiro _routes.json_ deve existir na raiz da pasta de artefactos de construção da app. Se a sua aplicação web incluir um passo de construção que copia ficheiros construídos de uma pasta específica para a sua pasta de artefactos de construção, então o ficheiro _routes.json_ precisa de existir nessa pasta específica.

A tabela que se segue lista a localização adequada para colocar o ficheiro _routes.json_ para uma série de quadros e bibliotecas JavaScript front-front-end.

|Enquadramento /biblioteca | Localização  |
|---------|----------|
| Angular | _ativos_   |
| Reagir   | _público_  |
| Svelte  | _público_   |
| Rio Vue     | _público_ |

## <a name="defining-routes"></a>Definição de rotas

As rotas são definidas no ficheiro _routes.json_ como uma variedade de regras de rota na `routes` propriedade. Cada regra é composta por um padrão de rota, juntamente com uma ou mais das propriedades de regra opcionais. Consulte o ficheiro de [rota de exemplo](#example-route-file) para exemplos de utilização.

| Propriedade de regras  | Necessário | Valor predefinido | Comentário                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Sim      | n/a          | O padrão de rota solicitado pelo chamador.<ul><li>[Os wildcards](#wildcards) são apoiados no final dos percursos. Por exemplo, o _administrador \* da rota/corresponde_ a qualquer rota sob o caminho de _administração._<li>O ficheiro padrão de uma rota é _index.html_.</ul>|
| `serve`        | Não       | n/a          | Define o ficheiro ou caminho devolvido do pedido. O caminho e o nome dos ficheiros podem ser diferentes do caminho solicitado. Se um `serve` valor for definido, então o caminho solicitado é utilizado. |
| `allowedRoles` | Não       | anónimo     | Uma série de nomes de papéis. <ul><li>Os caracteres válidos `a-z` `A-Z` incluem, e `0-9` `_` .<li>A função incorporada `anonymous` aplica-se a todos os utilizadores não autenticados.<li>A função incorporada `authenticated` aplica-se a qualquer utilizador registado.<li>Os utilizadores devem pertencer a pelo menos uma função.<li>As funções são correspondidas numa base _or._ Se um utilizador estiver em qualquer uma das funções listadas, então o acesso é concedido.<li>Os utilizadores individuais estão associados a funções através [de convites.](authentication-authorization.md)</ul> |
| `statusCode`   | Não       | 200           | A resposta do código de [estado HTTP](https://wikipedia.org/wiki/List_of_HTTP_status_codes) para o pedido. |

## <a name="securing-routes-with-roles"></a>Assegurar rotas com funções

As rotas são asseguradas adicionando um ou mais nomes de papéis na matriz de uma `allowedRoles` regra. Consulte o ficheiro de [rota de exemplo](#example-route-file) para exemplos de utilização.

Por padrão, cada utilizador pertence à `anonymous` função incorporada, e todos os utilizadores registados são membros da `authenticated` função. Por exemplo, para restringir uma rota a apenas utilizadores autenticados, adicione a função incorporada `authenticated` à `allowedRoles` matriz.

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

- Tem total controlo sobre nomes de papéis; não há uma lista principal à qual os seus papéis devem aderir.
- Os utilizadores individuais estão associados a papéis através [de convites.](authentication-authorization.md)

## <a name="wildcards"></a>Carateres universais

As regras do Wildcard correspondem a todos os pedidos sob um determinado padrão de rota. Se definir um `serve` valor na sua regra, o ficheiro ou caminho nomeado é servido como resposta.

Por exemplo, para implementar rotas para uma aplicação de calendário, pode mapear todos os URLs que se enquadram na rota do _calendário_ para servir um único ficheiro.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

O ficheiro _calendar.html_ pode então usar o encaminhamento do lado do cliente para servir uma visão diferente para variações de URL como `/calendar/january/1` , e `/calendar/2020` `/calendar/overview` .

Também pode garantir rotas com wildcards. No exemplo seguinte, qualquer ficheiro solicitado no âmbito da via _de administrador_ requer um utilizador autenticado que seja membro da função de _administrador._

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> Os pedidos de ficheiros referenciados por um ficheiro servido são avaliados apenas para verificações de autenticação. Por exemplo, os pedidos para um ficheiro CSS sob um caminho wildcard servem o ficheiro CSS, e não o que é definido como o `serve` ficheiro. O ficheiro CSS é servido desde que o utilizador cumpra os requisitos de autenticação exigidos.

## <a name="fallback-routes"></a>Rotas de recuo

As estruturas ou bibliotecas JavaScript front-end dependem frequentemente do encaminhamento do lado do cliente para navegação de aplicações web. Estas regras de encaminhamento do lado do cliente atualizam a localização da janela do navegador sem fazer pedidos de volta ao servidor. Se atualizar a página ou navegar diretamente para locais gerados pelas regras de encaminhamento do lado do cliente, é necessária uma rota de recuo do lado do servidor para servir a página HTML apropriada.

Uma rota de recuo comum é mostrada no seguinte exemplo:

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

A rota de recuo deve ser listada em último lugar nas suas regras de encaminhamento, uma vez que captura todos os pedidos não apanhados por regras previamente definidas.

## <a name="redirects"></a>Redirecionamentos

Pode utilizar códigos de estado [301](https://en.wikipedia.org/wiki/HTTP_301) e [302](https://en.wikipedia.org/wiki/HTTP_302) HTTP para redirecionar pedidos de uma rota para outra.

Por exemplo, a seguinte regra cria um redirecionamento 301 de _old-page.html_ para _new-page.html_.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Os redirecionamentos também trabalham com caminhos que não definem ficheiros distintos.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Páginas de erro personalizadas

Os utilizadores podem encontrar uma série de situações diferentes que podem resultar num erro. Utilizando a `platformErrorOverrides` matriz, pode fornecer uma experiência personalizada em resposta a estes erros. Consulte o ficheiro de rota de [exemplo](#example-route-file) para colocação da matriz no ficheiro _routes.json._

A tabela que se segue lista os erros da plataforma disponíveis sobreposições:

| Tipo de erro  | Código de estado de HTTP | Descrição |
|---------|---------|---------|
| `NotFound` | 404  | Uma página não é encontrada no servidor. |
| `Unauthenticated` | 401 | O utilizador não está registado com um fornecedor de [autenticação.](authentication-authorization.md) |
| `Unauthorized_InsufficientUserInformation` | 401 | A conta do utilizador no fornecedor de autenticação não está configurada para expor os dados necessários. Este erro pode ocorrer em situações como quando a aplicação pede ao fornecedor de autenticação o endereço de e-mail do utilizador, mas o utilizador optou por restringir o acesso ao endereço de e-mail. |
| `Unauthorized_InvalidInvitationLink` | 401 | Um convite expirou ou o utilizador seguiu um link de convite gerado para outro destinatário.  |
| `Unauthorized_MissingRoles` | 401 | O utilizador não é membro de uma função necessária. |
| `Unauthorized_TooManyUsers` | 401 | O site atingiu o número máximo de utilizadores, e o servidor está a limitar novas adições. Este erro está exposto ao cliente porque não há limite para o número de [convites](authentication-authorization.md) que pode gerar, e alguns utilizadores podem nunca aceitar o seu convite.|
| `Unauthorized_Unknown` | 401 | Há um problema desconhecido ao tentar autenticar o utilizador. Uma das razões para este erro pode ser que o utilizador não seja reconhecido porque não concedeu o consentimento à aplicação.|

## <a name="example-route-file"></a>Arquivo de rota de exemplo

O exemplo que se segue mostra como construir regras de rota para conteúdo estático e APIs num ficheiro _routes.json._ Algumas rotas utilizam a pasta do sistema [ _/.auth_ ](authentication-authorization.md) que acede a pontos finais relacionados com a autenticação.

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
      "serve": "/login"
    }
  ]
}
```

Os seguintes exemplos descrevem o que acontece quando um pedido corresponde a uma regra.

|Pedidos para...  | Resultado em... |
|---------|---------|---------|
| _/perfil_ | Os utilizadores autenticados são servidos o ficheiro _/profile/index.html._ Utilizadores não autenticados redirecionados para _/login_. |
| _/administração/relatórios_ | Os utilizadores autenticados na função de _administradores_ são servidos o ficheiro _/admin/reports/index.html._ Os utilizadores autenticados que não estejam na função de _administradores_ são servidos um erro<sup>401 1</sup>. Utilizadores não autenticados redirecionados para _/login_. |
| _/api/administrador_ | Os pedidos de utilizadores autenticados na função de _administradores_ são enviados para a API. Os utilizadores autenticados que não estão na função de _administradores_ e os utilizadores não autenticados são servidos um erro de 401. |
| _/clientes/contoso_ | Os utilizadores autenticados que pertencem aos _administradores_ ou _clientes \_ _ são servidos o ficheiro _/customers/contoso/index.html_ <sup>1</sup>. Os utilizadores autenticados que não estejam nas funções de _administradores_ ou _clientes \_ _ são servidos um erro de 401. Utilizadores não autenticados redirecionados para _/login_. |
| _/login_     | Os utilizadores não autenticados são desafiados a autenticar com o GitHub. |
| _/.auth/login/twitter_     | A autorização com o Twitter está desativada. O servidor responde com um erro de 404. |
| _/logout_     | Os utilizadores são saqueados de qualquer fornecedor de autenticação. |
| _/calendário/2020/01_ | O navegador é servido o ficheiro _/calendar.html._ |
| _/especiais_ | O navegador é redirecionado para _/ofertas._ |
| _/pasta desconhecida_     | O ficheiro _/custom-404.html_ é servido. |

<sup>1</sup> Pode fornecer uma página de erro personalizada definindo uma `Unauthorized_MissingRoles` regra na `platformErrorOverrides` matriz.

## <a name="restrictions"></a>Restrições

- O ficheiro _routes.json_ não pode ser superior a 100 KB
- O ficheiro _routes.json_ suporta um máximo de 50 funções distintas

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Autenticação e autorização de configuração](authentication-authorization.md)
