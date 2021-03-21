---
title: Configurar aplicativos web estáticos Azure
description: Aprenda a configurar rotas, impor regras de segurança e configurações globais para aplicações web estáticas Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: cshoe
ms.openlocfilehash: 324a8e75488d74fc6aa52e499b8dde616cd9beb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034052"
---
# <a name="configure-azure-static-web-apps"></a>Configurar aplicativos web estáticos Azure

A configuração para Azure Static Web Apps é definida na _staticwebapp.config.jsno_ ficheiro, que controla as seguintes definições:

- Encaminhamento
- Autenticação
- Autorização
- Regras de recuo
- Resposta HTTP sobrepõe-se
- Definições globais de cabeçalho HTTP
- Tipos de MIME personalizados

## <a name="file-location"></a>Localização do arquivo

A localização recomendada para o _staticwebapp.config.jsligado_ está no conjunto de pastas como o do ficheiro de fluxo `app_location` de [trabalho](./github-actions-workflow.md). No entanto, o ficheiro pode ser colocado em qualquer local dentro da pasta do código fonte da sua aplicação.

Consulte o ficheiro [de configuração](#example-configuration-file) de exemplo para obter mais detalhes.

> [!IMPORTANT]
> O [ _routes.jsno_ ficheiro](./routes.md) é ignorado se existir uma _staticwebapp.config.js._

## <a name="routes"></a>Rotas

As regras de rota permitem definir o padrão de URLs que permitem o acesso à sua aplicação à web. As rotas são definidas como uma série de regras de encaminhamento. Consulte o [ficheiro de configuração](#example-configuration-file) de exemplo para exemplos de utilização.

- As regras são executadas na ordem tal como aparecem na `routes` matriz.
- A avaliação das regras para no primeiro jogo- as regras de encaminhamento não estão acorrentadas.
- Tens controlo total sobre os nomes de papéis personalizados.
  - Há alguns nomes de papéis incorporados que incluem [`anonymous`](./authentication-authorization.md) e [`authenticated`](./authentication-authorization.md) .

O encaminhamento diz respeito a conceitos de autenticação (identificação do utilizador) e autorização (atribuindo capacidades ao utilizador). Certifique-se de que lê o guia [de autenticação e autorização](authentication-authorization.md) juntamente com este artigo.

O ficheiro predefinido para conteúdo estático é o *ficheiroindex.html.*

## <a name="defining-routes"></a>Definição de rotas

Cada regra é composta por um padrão de rota, juntamente com uma ou mais das propriedades de regras opcionais. Consulte o [ficheiro de configuração](#example-configuration-file) de exemplo para exemplos de utilização.

| Propriedade de regra  | Necessário | Valor predefinido | Comentário                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Yes      | n/a          | O padrão de rota solicitado pelo chamador.<ul><li>[Os wildcards](#wildcards) são suportados no final dos caminhos de rota.<ul><li>Por exemplo, a rota _\* admin/corresponde_ a qualquer rota sob o caminho _administrativo._</ul></ul>|
| `rewrite`        | No       | n/a          | Define o ficheiro ou caminho devolvido do pedido.<ul><li>É mutuamente exclusivo de uma `redirect` regra<li>As regras de reescrita não alteram a localização do navegador.<li>Os valores devem ser relativos à raiz da app</ul>  |
| `redirect`        | No       | n/a          | Define o destino de redirecionamento de ficheiro ou caminho para um pedido.<ul><li>É mutuamente exclusivo de uma `rewrite` regra.<li>As regras de redirecionamento alteram a localização do navegador.<li>O código de resposta predefinido é um [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (redirecionamento temporário), mas pode ser sobrepõe-se a um [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (redirecionamento permanente).</ul> |
| `allowedRoles` | No       | anónimo     | Define uma lista de nomes de papéis necessários para aceder a uma rota. <ul><li>Os caracteres válidos `a-z` `A-Z` incluem, , e `0-9` `_` .<li>O papel [`anonymous`](./authentication-authorization.md) incorporado, aplica-se a todos os utilizadores não autenticados<li>A função [`authenticated`](./authentication-authorization.md) incorporada, aplica-se a qualquer utilizador com sessão iniciada.<li>Os utilizadores devem pertencer a pelo menos uma função.<li>As funções são correspondidas numa base _de OR._<ul><li>Se um utilizador estiver em alguma das funções listadas, então o acesso é concedido.</ul><li>Os utilizadores individuais estão associados a funções através de [convites.](authentication-authorization.md)</ul> |
| `headers`<a id="route-headers"></a> | No | n/a | Conjunto de [cabeçalhos HTTP adicionados](https://developer.mozilla.org/docs/Web/HTTP/Headers) à resposta. <ul><li>Os cabeçalhos específicos [`globalHeaders`](#global-headers) da rota sobrepõem-se quando o cabeçalho específico da rota é o mesmo que o cabeçalho global está na resposta.<li>Para remover um cabeçalho, desa um valor para uma corda vazia.</ul> |
| `statusCode`   | No       | `200`, `301` ou `302` para redirecionamentos | O código de [estado HTTP](https://developer.mozilla.org/docs/Web/HTTP/Status) da resposta. |
| `methods` | No | Todos os métodos | Lista de métodos de pedido que correspondem a uma rota. Os métodos disponíveis incluem: `GET` , , , , , , , , `HEAD` e `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` `PATCH` . |

Cada imóvel tem um propósito específico no pipeline de pedido/resposta.

| Objetivo | Propriedades |
|---|---|
| Rotas de correspondência | `route`, `methods` |
| Autorizar depois de uma rota ser igualada | `allowedRoles` |
| Processo após regra é combinado e autorizado | `rewrite` (modifica o pedido) <br><br>`redirect`, `headers` `statusCode` (modifica a resposta) |

## <a name="securing-routes-with-roles"></a>Assegurar rotas com funções

As rotas são asseguradas adicionando um ou mais nomes de papéis na matriz de uma `allowedRoles` regra, e os utilizadores estão associados a funções [personalizadas](./authentication-authorization.md)através de convites . Consulte o [ficheiro de configuração](#example-configuration-file) de exemplo para exemplos de utilização.

Por predefinição, todos os utilizadores pertencem à `anonymous` função incorporada, e todos os utilizadores com login são membros do `authenticated` papel.

Por exemplo, para restringir uma rota apenas para utilizadores autenticados, adicione a função incorporada `authenticated` à `allowedRoles` matriz.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Pode criar novos papéis conforme necessário na `allowedRoles` matriz. Para restringir uma rota para apenas administradores, você poderia definir o seu próprio papel chamado `administrator` , na `allowedRoles` matriz.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Tem total controlo sobre os nomes das funções; não há nenhuma lista à qual os seus papéis devem aderir.
- Os utilizadores individuais estão associados a funções através de [convites.](authentication-authorization.md)

## <a name="wildcards"></a>Carateres universais

As regras wildcard correspondem a todos os pedidos num padrão de rota, são suportadas apenas no final de um caminho, e podem ser filtradas por extensão de ficheiro. Consulte o [ficheiro de configuração](#example-configuration-file) de exemplo para exemplos de utilização.

Por exemplo, para implementar rotas para uma aplicação de calendário, você pode reescrever todos os URLs que se enquadram na rota do _calendário_ para servir um único ficheiro.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

O ficheiro _calendar.html_ pode então utilizar o encaminhamento do lado do cliente para servir uma visão diferente para variações de URL como , `/calendar/january/1` e `/calendar/2020` `/calendar/overview` .

Pode filtrar os jogos wildcard por extensão de ficheiro. Por exemplo, se quiser adicionar uma regra que apenas corresponda aos ficheiros HTML num determinado caminho, poderá criar a seguinte regra:

```json
{
  "route": "/articles/*.html",
  "headers" : {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Para filtrar em várias extensões de ficheiros, inclui as opções em aparelhos encaracolados, como mostra este exemplo:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Os casos de utilização comum para rotas wildcard incluem:

- Servir um arquivo específico para um padrão de caminho inteiro
- Mapear diferentes métodos HTTP para um padrão de caminho inteiro
- Aplicação das regras de autenticação e autorização
- Implementar regras especializadas em caching

## <a name="fallback-routes"></a>Rotas de recuo

As aplicações de página única dependem frequentemente do encaminhamento do lado do cliente. Estas regras de encaminhamento do lado do cliente atualizam a localização da janela do navegador sem fazer pedidos de volta ao servidor. Se atualizar a página ou navegar diretamente para URLs gerados pelas regras de encaminhamento do lado do cliente, é necessária uma rota de recuo do lado do servidor para servir a página HTML apropriada (que é geralmente a _index.html_ para a sua aplicação do lado do cliente).

Pode configurar a sua aplicação para utilizar regras que implementem uma rota de retorno, como mostra o exemplo que utiliza um wildcard de caminho com filtro de ficheiros:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

A estrutura de ficheiros de exemplo abaixo, os seguintes resultados são possíveis com esta regra.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| Pedidos para... | devoluções... | com o estado... |
| --- | --- | --- |
| */sobre/* | O ficheiro */index.html* | `200` |
| */imagens/logo.png* | O arquivo de imagem  | `200` |
| */imagens/ícone.svg* | O ficheiro */index.html* - uma vez que a extensão do ficheiro *svg* não está listada no `/images/*.{png,jpg,gif}` filtro   | `200` |
| */imagens/unknown.png* | Ficheiro não encontrado erro  | `404` |
| */css/desconhecido.css* | Ficheiro não encontrado erro  | `404` |
| */css/global.css* | O arquivo de folha de estilo | `200` |
| Qualquer outro ficheiro fora das *pastas /imagens* ou */css* | O ficheiro */index.html* | `200` |

## <a name="global-headers"></a>Cabeçalhos globais

A `globalHeaders` secção fornece um conjunto de [cabeçalhos HTTP aplicados](https://developer.mozilla.org/docs/Web/HTTP/Headers) a cada resposta, salvo ultrapassagens por uma regra de cabeçalho de [rota,](#route-headers) caso contrário a união de ambos os cabeçalhos da rota e os cabeçalhos globais são devolvidos.

Consulte o [ficheiro de configuração](#example-configuration-file) de exemplo para exemplos de utilização.

Para remover um cabeçalho, desa um valor para uma corda vazia `""` ().

Alguns casos de uso comum para cabeçalhos globais incluem:

- Regras personalizadas de colocação em cache
- Aplicação das políticas de segurança
- Definições de codificação

## <a name="response-overrides"></a>Sobreposições de resposta

A `responseOverrides` secção oferece a oportunidade de definir uma resposta personalizada quando o servidor devolveu de outra forma um código de erro. Consulte o [ficheiro de configuração](#example-configuration-file) de exemplo para exemplos de utilização.

Os seguintes códigos HTTP estão disponíveis para anular:

| Código de Estado | Significado | Causa possível |
| --- | --- | --- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Mau pedido | Link de convite inválido |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Não autorizado | Pedido de páginas restritas enquanto não autenticado |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Proibido |<ul><li>O utilizador está a iniciar sessão, mas não tem as funções necessárias para ver a página.<li>O utilizador está a iniciar sessão, mas o tempo de execução não consegue obter os dados do utilizador das suas alegações de identidade.<li>Há muitos utilizadores ligados ao site com funções personalizadas, pelo que o tempo de execução não pode iniciar sessão no utilizador.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Não encontrado | Arquivo não encontrado |

A configuração de exemplo a seguir demonstra como substituir um código de erro.

```json
{
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html",
            "statusCode": 200
        },
        "401": {
            "statusCode": 302,
            "redirect": "/login"
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html",
            "statusCode": 200
        },
        "404": {
            "rewrite": "/custom-404.html",
            "statusCode": 200
        }
    }
}
```

## <a name="example-configuration-file"></a>Ficheiro de configuração de exemplo

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
            "route": "/images/*",
            "headers": {
                "cache-control": "must-revalidate, max-age=15770000"
            }
        },
        {
            "route": "/api/*",
            "methods": [ "GET" ],
            "allowedRoles": ["registeredusers"]
        },
        {
            "route": "/api/*",
            "methods": [ "PUT", "POST", "PATCH", "DELETE" ],
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/api/*",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/customers/contoso",
            "allowedRoles": ["administrator", "customers_contoso"]
        },
        {
            "route": "/login",
            "rewrite": "/.auth/login/github"
        },
        {
            "route": "/.auth/login/twitter",
            "statusCode": 404,
        },
        {
            "route": "/logout",
            "redirect": "/.auth/logout"
        },
        {
            "route": "/calendar/*",
            "rewrite": "/calendar.html"
        },
        {
            "route": "/specials",
            "redirect": "/deals",
            "statusCode": 301
        }
    ],
    "navigationFallback": {
      "rewrite": "index.html",
      "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
    },
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html"
        },
        "401": {
            "redirect": "/login",
            "statusCode": 302
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html"
        },
        "404": {
            "rewrite": "/404.html"
        }
    },
    "globalHeaders": {
        "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
    },
    "mimeTypes": {
        ".json": "text/json"
    },
}
```

Com base na configuração acima, reveja os seguintes cenários.

| Pedidos para... | resulta em... |
| --- | --- |
| _/perfil_ | Os utilizadores autenticados são servidos o ficheiro _/perfil/index.html._ Os utilizadores não autenticados são redirecionados para _/login_. |
| _/administrador/_ | Os utilizadores autenticados na função _de administrador_ são servidos o ficheiro _/administrador/index.html._ Os utilizadores autenticados que não estão na função _de administrador_ são servidos um `403` erro <sup>1</sup>. Os utilizadores não autenticados são redirecionados para _/login_. |
| _/logo.png_ | Serve a imagem com uma regra de cache personalizada onde a idade máxima é de pouco mais de 182 dias (15.770.000 segundos). |
| _/api/administrador_ | `GET` os pedidos de utilizadores autenticados na _função de registo_ são enviados para a API. Os utilizadores autenticados que não estão na _função de registo_ e os utilizadores não autenticados são notificados de um `401` erro.<br/><br/>`POST`, `PUT` `PATCH` , e `DELETE` pedidos de utilizadores autenticados na função _de administrador_ são enviados para a API. Os utilizadores autenticados que não estão na função _de administrador_ e os utilizadores não autenticados são notificados de um `401` erro. |
| _/clientes/contoso_ | Os utilizadores autenticados que pertencem às funções do _administrador_ ou _dos \_ clientes_ são servidos o ficheiro _l /clientes/contoso/index.htm._ Os utilizadores autenticados que não estão nas funções _do administrador_ ou _\_ dos clientes_ são servidos um `403` erro <sup>1</sup>. Os utilizadores não autenticados são redirecionados para _/login_. |
| _/login_ | Os utilizadores não autenticados são desafiados a autenticar com o GitHub. |
| _/.auth/login/twitter_ | Como a autorização com o Twitter é desativada pela regra da rota, o `404` erro é devolvido, que recai em servir _/index.html_ com um código de `200` estado. |
| _/logout_ | Os utilizadores são registados fora de qualquer fornecedor de autenticação. |
| _/calendário/2021/01_ | O navegador é servido o ficheiro _/calendar.html._ |
| _/especiais_ | O navegador é redirecionado permanentemente para _/ofertas._ |
| _/data.jsem_ | O ficheiro servido com o `text/json` tipo MIME. |
| _/sobre_, ou qualquer pasta que corresponda aos padrões de encaminhamento do lado do cliente | O ficheiro _/index.html_ é servido com um `200` código de estado. |
| Um ficheiro inexistente na _/imagens/pasta_ | Um `404` erro. |

<sup>1</sup> Pode fornecer uma página de erro personalizada utilizando uma [regra de substituição de resposta](#response-overrides).

## <a name="restrictions"></a>Restrições

Existem as seguintes restrições para o _staticwebapps.config.jsarquivado._

- O tamanho do ficheiro máximo é de 100 KB
- Max de 50 papéis distintos

Consulte o [artigo Quotas](quotas.md) para restrições e limitações gerais.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Autenticação e autorização de instalação](authentication-authorization.md)
