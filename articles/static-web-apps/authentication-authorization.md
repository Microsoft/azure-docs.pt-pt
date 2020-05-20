---
title: Autenticação e autorização para aplicações web estáticas do Azure
description: Aprenda a usar diferentes fornecedores de autorização para garantir a sua aplicação estática.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1ac1df402c25c0f6e5f07ce8d9631c01c0fa504c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655261"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Autenticação e autorização para pré-visualização de aplicações web estáticas do Azure

As Web Apps Estáticas Azure dinamizam a experiência de autenticação gerindo a autenticação com os seguintes fornecedores:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

Convites [específicos](#invitations) do fornecedor associam os utilizadores a funções, e os utilizadores autorizados têm acesso a [rotas](routes.md) por regras definidas no ficheiro _routes.json._

Todos os fornecedores de autenticação estão ativados por defeito. Para restringir um fornecedor de autenticação, [bloqueie o acesso](#block-an-authorization-provider) com uma regra de rota personalizada.

Os tópicos de autenticação e autorização sobrepõem-se significativamente aos conceitos de encaminhamento. Leia o guia de [encaminhamento](routes.md) juntamente com este artigo.

## <a name="roles"></a>Funções

Todos os utilizadores que acedem a uma aplicação web estática pertencem a um ou mais papéis.  Existem duas funções incorporadas a que os utilizadores podem pertencer:

- **anónimo**: Todos os utilizadores pertencem automaticamente à função _anónima._
- **autenticado**: Todos os utilizadores que estão registados pertencem à função _autenticada._

Para além das funções incorporadas, pode criar novas funções, atribuí-las aos utilizadores através de convites, e remeti-las no ficheiro _routes.json._

## <a name="role-management"></a>Gestão de funções

### <a name="add-a-user-to-a-role"></a>Adicione um utilizador a um papel

Para adicionar utilizadores ao seu site, gera convites que lhe permitem associar os utilizadores a funções específicas. As funções são definidas e mantidas no ficheiro _routes.json._

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Criar um convite

Os convites são específicos para os fornecedores individuais de autorização, por isso considere as necessidades da sua app, pois seleciona quais os fornecedores a apoiar. Alguns fornecedores expõem o endereço de e-mail de um utilizador, enquanto outros apenas fornecem o nome de utilizador do site.

<a name="provider-user-details" id="provider-user-details"></a>

| Provedor de autorização | Expõe o utilizador  |
| ---------------------- | ----------------- |
| Azure Active Directory | endereço de e-mail     |
| Facebook               | endereço de e-mail     |
| GitHub                 | o nome de utilizador          |
| Google<sup>1</sup>     | endereço de e-mail     |
| Twitter                | o nome de utilizador          |

1. Navegue para um recurso de Aplicações Web Estáticas no [portal Azure.](https://portal.azure.com)
1. Em _Definições,_ clique na **Gestão de Funções**.
1. Clique no botão **Convidar.**
1. Selecione um fornecedor de _Autorização_ na lista de opções.
1. Adicione o nome de utilizador ou o endereço de e-mail do destinatário na caixa de _detalhes do Convite._
    - Para o GitHub e o Twitter, introduza o nome de utilizador. Para todos os outros, insira o endereço de e-mail do destinatário.
1. Selecione o domínio do seu site estático a partir da queda do _Domínio._
    - O domínio que seleciona é o domínio que aparece no convite. Se tiver um domínio personalizado associado ao seu site, provavelmente deseja escolher o domínio personalizado.
1. Adicione uma lista separada de nomes de papéis na caixa _role._
1. Insira o número máximo de horas que pretende que o convite permaneça válido.
    - O limite máximo possível é de 168 horas, que é de 7 dias.
1. Clique no botão **Generate** (Gerar).
1. Copie o link da caixa de _ligação Convidar._
1. Envie por e-mail o link de convite para a pessoa que está a conceder acesso à sua aplicação.

Quando o utilizador clica no link no convite, é solicitado a fazer login com a conta correspondente. Uma vez iniciado o login com sucesso, o utilizador está associado às funções selecionadas.

> [!CAUTION]
> Certifique-se de que as regras da rota não entram em conflito com os seus fornecedores de autenticação selecionados. Bloquear um fornecedor com uma regra de rota impediria os utilizadores de aceitarconvites.

### <a name="update-role-assignments"></a>Atribuição de funções de atualização

1. Navegue para um recurso de Aplicações Web Estáticas no [portal Azure.](https://portal.azure.com)
1. Em _Definições,_ clique na **Gestão de Funções**.
1. Clique no utilizador da lista.
1. Editar a lista de papéis na caixa _role._
1. Clique no botão **Atualizar.**

### <a name="remove-user"></a>Remover utilizador

1. Navegue para um recurso de Aplicações Web Estáticas no [portal Azure.](https://portal.azure.com)
1. Em _Definições,_ clique na **Gestão de Funções**.
1. Localize o utilizador na lista.
1. Verifique a caixa de verificação na fila do utilizador.
1. Clique no botão **Eliminar**.

Ao remover um utilizador, tenha em mente os seguintes itens:

1. Remover um utilizador invalida as suas permissões.
1. A propagação mundial pode demorar alguns minutos.
1. Se o utilizador for adicionado de volta à aplicação, as [ `userId` alterações](user-information.md).

## <a name="remove-personal-identifying-information"></a>Remover informações pessoais de identificação

Quando concede o consentimento a uma aplicação como utilizador final, a aplicação tem acesso ao seu endereço de e-mail ou ao seu nome de utilizador dependendo do fornecedor de identidade. Uma vez que esta informação é fornecida, o proprietário do pedido decide como gerir informações de identificação pessoal.

Os utilizadores finais precisam de contactar administradores de aplicações web individuais para revogar esta informação dos seus sistemas.

Para remover pessoalmente informações de identificação da plataforma Azure Static Web Apps, e impedir que a plataforma forneça esta informação sobre futuros pedidos, submeta um pedido utilizando o URL:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Para evitar que a plataforma forneça esta informação sobre futuros pedidos a aplicações individuais, submeta um pedido ao seguinte URL:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Pasta do sistema

As Aplicações Web Estáticas do Azure utilizam a pasta do `/.auth` sistema para fornecer acesso a APIs relacionados com autorização. Em vez de expor qualquer uma das rotas sob a `/.auth` pasta diretamente para os utilizadores finais, considere criar regras de [encaminhamento](routes.md) para criar URLs amigáveis.

## <a name="login"></a>Iniciar sessão

Utilize a tabela seguinte para encontrar a rota de login específica do fornecedor.

| Provedor de autorização | Rota de login             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Por exemplo, para iniciar sessão com o GitHub pode incluir uma ligação de login como o seguinte corte:

```html
<a href="/.auth/login/github">Login</a>
```

Se optou por apoiar mais do que um fornecedor, então precisa expor um link específico para cada um no seu website.

Pode utilizar uma regra de [rota](routes.md) para mapear um fornecedor predefinido para uma rota amigável como _/login_.

```json
{
  "route": "/login",
  "serve": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Redirecionamento de login de posta

Se pretender que um utilizador regresse a uma página específica após o login, forneça um URL no parâmetro de `post_login_redirect_uri` corda de consulta.


## <a name="logout"></a>Terminar sessão

A `/.auth/logout` rota regista os utilizadores a partir do site. Pode adicionar um link à navegação do seu site para permitir que o utilizador faça login como mostrado no exemplo seguinte.

```html
<a href="/.auth/logout">Log out</a>
```

Você pode usar uma regra de [rota](routes.md) para mapear uma rota amigável como _/logout_.

```json
{
  "route": "/logout",
  "serve": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Redirecionamento de registo de posta

Se pretender que um utilizador regresse a uma página específica após o início do início, forneça um URL no parâmetro de `post_logout_redirect_uri` corda de consulta.

## <a name="block-an-authorization-provider"></a>Bloquear um provedor de autorização

Pode querer restringir a sua aplicação de utilização de um fornecedor de autorização. Por exemplo, a sua aplicação pode querer normalizar apenas em [fornecedores que exponham endereços de e-mail](#provider-user-details).

Para bloquear um fornecedor, pode criar regras de [rota](routes.md) para devolver um 404 para pedidos à rota específica do fornecedor bloqueada. Por exemplo, para restringir o Twitter como fornecedor, adicione a seguinte regra de rota.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aceder aos dados de autenticação e autorização do utilizador](user-information.md)

<sup>1</sup> Pendente de certificação externa.
