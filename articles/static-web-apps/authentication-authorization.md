---
title: Autenticação e autorização para aplicações web estáticas Azure
description: Aprenda a usar diferentes fornecedores de autorização para proteger a sua aplicação estática.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 9c8dd723c9cde5c0534d9fd5ca4084c7ed15d213
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218639"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Autenticação e autorização para Pré-visualização das Aplicações Web Estáticas do Azure

A Azure Static Web Apps dinamiza a experiência de autenticação gerindo a autenticação com os seguintes fornecedores:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

Convites [específicos](#invitations) do fornecedor associam os utilizadores a funções, e os utilizadores autorizados têm acesso a [rotas](routes.md) por regras definidas no _routes.jsem_ arquivo.

Todos os fornecedores de autenticação estão habilitados por predefinição. Para restringir um fornecedor de autenticação, [bloqueie o acesso](#block-an-authorization-provider) com uma regra de rota personalizada.

Os tópicos de autenticação e autorização sobrepõem-se significativamente aos conceitos de encaminhamento. Certifique-se de que lê o [guia de encaminhamento](routes.md) juntamente com este artigo.

## <a name="roles"></a>Funções

Todos os utilizadores que acedem a uma aplicação web estática pertencem a uma ou mais funções.  Existem duas funções incorporadas a que os utilizadores podem pertencer:

- **anónimo**: Todos os utilizadores pertencem automaticamente à função _anónima._
- **autenticado**: Todos os utilizadores que estejam registados pertencem à função _autenticada._

Além das funções incorporadas, pode criar novas funções, atribuí-las aos utilizadores através de convites e fazê-las referenciar no _routes.jsem_ ficheiro.

## <a name="role-management"></a>Gestão de funções

### <a name="add-a-user-to-a-role"></a>Adicionar um utilizador a uma função

Para adicionar utilizadores ao seu site, gera convites que lhe permitem associar utilizadores a funções específicas. As funções são definidas e mantidas no _routes.jsarquivado._

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Criar um convite

Os convites são específicos para os fornecedores individuais de autorização, por isso considere as necessidades da sua app à medida que seleciona quais os fornecedores a apoiar. Alguns fornecedores expõem o endereço de e-mail de um utilizador, enquanto outros apenas fornecem o nome de utilizador do site.

<a name="provider-user-details" id="provider-user-details"></a>

| Prestador de autorização | Expõe a de um utilizador  |
| ---------------------- | ----------------- |
| Azure Active Directory | endereço de e-mail     |
| Facebook               | endereço de e-mail     |
| GitHub                 | nome de utilizador          |
| Google<sup>1</sup>     | endereço de e-mail     |
| Twitter                | nome de utilizador          |

1. Navegue para um recurso estático de Aplicações Web no [portal Azure](https://portal.azure.com).
1. Em _Definições_, clique em **Gestão de Funções**.
1. Clique no botão **Convidar.**
1. Selecione um fornecedor de _autorização_ na lista de opções.
1. Adicione o nome de utilizador ou o endereço de e-mail do destinatário na caixa de _detalhes do Convite._
    - Para GitHub e Twitter, introduza o nome de utilizador. Para todos os outros, insira o endereço de e-mail do destinatário.
1. Selecione o domínio do seu site estático a partir do drop-down do _Domínio._
    - O domínio que seleciona é o domínio que aparece no convite. Se tiver um domínio personalizado associado ao seu site, provavelmente deseja escolher o domínio personalizado.
1. Adicione uma lista separada de vírgulas de nomes de papéis na caixa _Role._
1. Introduza o número máximo de horas que pretende que o convite permaneça válido.
    - O limite máximo possível é de 168 horas, ou seja, 7 dias.
1. Clique no botão **Generate** (Gerar).
1. Copie o link da caixa _de link 'Convite'._
1. Envie um e-mail com o link de convite para a pessoa que está a conceder acesso à sua aplicação.

Quando o utilizador clica no link no convite, é-lhes solicitado que faça login com a conta correspondente. Uma vez iniciado com sucesso, o utilizador está associado às funções selecionadas.

> [!CAUTION]
> Certifique-se de que as suas regras de rota não entram em conflito com os seus fornecedores de autenticação selecionados. Bloquear um fornecedor com uma regra de rota impediria os utilizadores de aceitar convites.

### <a name="update-role-assignments"></a>Atualizar atribuições de funções

1. Navegue para um recurso estático de Aplicações Web no [portal Azure](https://portal.azure.com).
1. Em _Definições_, clique em **Gestão de Funções**.
1. Clique no utilizador da lista.
1. Editar a lista de papéis na caixa _Role._
1. Clique no botão **'Actualização'.**

### <a name="remove-user"></a>Remover utilizador

1. Navegue para um recurso estático de Aplicações Web no [portal Azure](https://portal.azure.com).
1. Em _Definições_, clique em **Gestão de Funções**.
1. Localize o utilizador na lista.
1. Verifique a caixa de verificação na linha do utilizador.
1. Clique no botão **Eliminar**.

Ao remover um utilizador, tenha em mente os seguintes itens:

1. A remoção de um utilizador invalida as suas permissões.
1. A propagação mundial pode demorar alguns minutos.
1. Se o utilizador for adicionado de volta à aplicação, a [ `userId` alteração muda.](user-information.md)

## <a name="remove-personal-identifying-information"></a>Remover informações de identificação pessoal

Quando concede o consentimento a uma aplicação como utilizador final, a aplicação tem acesso ao seu endereço de e-mail ou ao seu nome de utilizador, dependendo do fornecedor de identidade. Uma vez fornecida esta informação, o proprietário do pedido decide como gerir pessoalmente a informação de identificação.

Os utilizadores finais precisam de contactar administradores de aplicações web individuais para revogar estas informações dos seus sistemas.

Para remover pessoalmente informações da plataforma Azure Static Web Apps e impedir que a plataforma forneça estas informações sobre pedidos futuros, envie um pedido utilizando o URL:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Para evitar que a plataforma forneça estas informações sobre futuros pedidos a aplicações individuais, envie um pedido para o seguinte URL:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Pasta do sistema

A azure Static Web Apps usa a pasta do `/.auth` sistema para fornecer acesso a APIs relacionados com a autorização. Em vez de expor qualquer uma das rotas sob a `/.auth` pasta diretamente aos utilizadores finais, considere criar [regras de encaminhamento](routes.md) para criar URLs amigáveis.

## <a name="login"></a>Iniciar sessão

Utilize a seguinte tabela para encontrar a rota de login específica do fornecedor.

| Prestador de autorização | Rota de login             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Por exemplo, para iniciar sessão com o GitHub pode incluir um link de login como o seguinte corte:

```html
<a href="/.auth/login/github">Login</a>
```

Se escolheu apoiar mais do que um fornecedor, então tem de expor um link específico do fornecedor para cada um no seu website.

Pode utilizar uma [regra de rota](./configuration.md#routes) para mapear um fornecedor predefinido para uma rota amigável como _/login_.

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Redirecionamento de login

Se pretender que um utilizador regresse a uma página específica após o início de sessão, forneça um URL qualificado completo no `post_login_redirect_uri` parâmetro de cadeia de consulta.

Por exemplo:

```html
<a href="/.auth/login/github?post_login_redirect_uri=https://zealous-water.azurestaticapps.net/success">Login</a>
```

## <a name="logout"></a>Terminar sessão

A `/.auth/logout` rota regista os utilizadores fora do site. Pode adicionar um link à navegação do seu site para permitir que o utilizador faça login como mostrado no exemplo seguinte.

```html
<a href="/.auth/logout">Log out</a>
```

Você pode usar uma [regra de rota](./configuration.md#routes) para mapear uma rota amigável como _/logout_.

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Redirecionamento de logout post

Se pretender que um utilizador regresse a uma página específica após o início de sê-lo, forneça um URL no `post_logout_redirect_uri` parâmetro de cadeia de consulta.

## <a name="block-an-authorization-provider"></a>Bloquear um fornecedor de autorização

Pode querer restringir a sua aplicação de usar um fornecedor de autorização. Por exemplo, a sua aplicação pode querer normalizar apenas em [fornecedores que exponam endereços de e-mail.](#provider-user-details)

Para bloquear um fornecedor, pode criar [regras](routes.md) de rota para devolver um 404 para pedidos à rota específica do fornecedor bloqueada. Por exemplo, para restringir o Twitter como fornecedor, adicione a seguinte regra de rota.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Restrições

Consulte o [artigo Quotas](quotas.md) para restrições e limitações gerais.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aceder aos dados de autenticação e autorização do utilizador](user-information.md)

<sup>1</sup> Pendente de certificação externa.
