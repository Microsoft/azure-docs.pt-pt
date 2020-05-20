---
title: Autenticação de Diretório Ativo - Base de Dados Azure para PostgreSQL - Servidor Único
description: Conheça os conceitos de Diretório Ativo Azure para autenticação com Base de Dados Azure para PostgreSQL - Servidor Único
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8fa9a4ebe64880ae136a8460d35200fb84abb6cf
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660178"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Utilizar o Diretório Ativo Azure para autenticação com postgreSQL

A autenticação do Microsoft Azure Ative Directory (Azure AD) é um mecanismo de ligação à Base de Dados Azure para postgreSQL utilizando identidades definidas em AD Azure.
Com a autenticação da AD Azure, pode gerir as identidades dos utilizadores da base de dados e outros serviços da Microsoft numa localização central, o que simplifica a gestão da permissão.

Os benefícios da utilização do Azure AD incluem:

- Autenticação de utilizadores em serviços Azure de forma uniforme
- Gestão das políticas de password e rotação de passwords num único local
- Múltiplas formas de autenticação suportadas pelo Azure Ative Directory, que podem eliminar a necessidade de armazenar senhas
- Os clientes podem gerir permissões de base de dados utilizando grupos externos (Azure AD).
- A autenticação Azure AD utiliza funções de base de dados PostgreSQL para autenticar identidades ao nível da base de dados
- Suporte da autenticação baseada em token para aplicações que liguem à Base de Dados Azure para PostgreSQL

Para configurar e utilizar a autenticação do Diretório Ativo Azure, utilize o seguinte processo:

1. Crie e povoeo Diretório Ativo Azure com as identidades dos utilizadores, conforme necessário.
2. Associar opcionalmente ou alterar opcionalmente o Diretório Ativo atualmente associado à sua subscrição Azure.
3. Crie um administrador de AD Azure para a Base de Dados Azure para o servidor PostgreSQL.
4. Crie utilizadores de bases de dados na sua base de dados mapeada para identidades da AD Azure.
5. Ligue-se à sua base de dados recuperando um símbolo para uma identidade azure ad e iniciando sessão.

> [!NOTE]
> Para aprender a criar e povoar o Azure AD, e depois configurar o Azure AD com base de dados Azure para PostgreSQL, consulte [configurar e iniciar sessão com a Azure AD para Azure Database para PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="architecture"></a>Arquitetura

O diagrama de alto nível que se segue resume como funciona a autenticação utilizando a autenticação Azure AD com base de dados Azure para PostgreSQL. As setas indicam vias de comunicação.

![fluxo de autenticação][1]

## <a name="administrator-structure"></a>Estrutura de administrador

Ao utilizar a autenticação AD Azure, existem duas contas de Administrador para o servidor PostgreSQL; o administrador original da PostgreSQL e o administrador da AD Azure. Apenas o administrador com base numa conta Azure AD pode criar o primeiro utilizador de base de dados Azure AD numa base de dados de utilizador. O login do administrador da AD Azure pode ser um utilizador de Anúncios Azure ou um grupo Azure AD. Quando o administrador é uma conta de grupo, pode ser usado por qualquer membro do grupo, permitindo vários administradores da AD Azure para o servidor PostgreSQL. A utilização de uma conta de grupo como administrador aumenta a capacidade de gestão, permitindo-lhe adicionar e remover centralmente os membros do grupo em AD Azure sem alterar os utilizadores ou permissões no servidor PostgreSQL. Apenas um administrador da AD Azure (utilizador ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração][2]

## <a name="permissions"></a>Permissões

Para criar novos utilizadores que possam autenticar com AD Azure, deve ter o papel na base de `azure_ad_admin` dados. Esta função é atribuída configurando a conta do Administrador AD Azure para uma base de dados azure específica para servidor PostgreSQL.

Para criar um novo utilizador de base de dados Azure AD, deve ligar-se como administrador da AD Azure. Isto é demonstrado em [Configure e Login com Azure AD para Base de Dados Azure para PostgreSQL](howto-configure-sign-in-aad-authentication.md).

Qualquer autenticação Azure AD só é possível se o administrador da AD Azure tiver sido criado para a Base de Dados Azure para postgreSQL. Se o administrador do Azure Ative Directory foi removido do servidor, os atuais utilizadores do Azure Ative Directory criados anteriormente já não podem ligar-se à base de dados utilizando as suas credenciais de Diretório Ativo Azure.

## <a name="connecting-using-azure-ad-identities"></a>Ligação utilizando identidades da AD Azure

A autenticação do Diretório Ativo Azure suporta os seguintes métodos de ligação a uma base de dados utilizando identidades da AD Azure:

- Senha de direção ativa azure
- Diretório Ativo Azure Integrado
- Diretório Ativo Azure Universal com MFA
- Utilização de certificados de pedido de diretório ativo ou segredos de cliente

Depois de autenticar contra o Diretório Ativo, então recupere um símbolo. Esta ficha é a sua senha para iniciar sessão.

> [!NOTE]
> Para mais detalhes sobre como se conectar com um token Ative Diretório, consulte [Configure e inscreva-se com a Azure AD para Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a capacidade de gestão, recomendamos que provisão de um grupo Azure AD dedicado como administrador.
- Apenas um administrador de AD Azure (um utilizador ou grupo) pode ser configurado para uma Base de Dados Azure para servidor PostgreSQL a qualquer momento.
- Apenas um administrador de AD Azure para postgresQL pode inicialmente ligar-se à Base de Dados Azure para PostgreSQL utilizando uma conta azure Ative Directory. O administrador de Diretório Ativo pode configurar os utilizadores subsequentes da base de dados Azure AD.
- Se um utilizador for eliminado do Azure AD, esse utilizador deixará de poder autenticar com a AD Azure, pelo que deixará de ser possível adquirir um sinal de acesso para esse utilizador. Neste caso, embora a função correspondente ainda esteja na base de dados, não será possível ligar-se ao servidor com essa função.
> [!NOTE]
> O login com o utilizador da AD Azure eliminado ainda pode ser feito até que o token expire (até 60 minutos a partir da emissão de fichas).  Se também remover o utilizador da Base de Dados Azure para postgreSQL, este acesso será revogado imediatamente.
- Se o administrador da AD Azure for removido do servidor, o servidor deixará de estar associado a um inquilino da AD Azure, pelo que todos os logins da AD Azure serão desativados para o servidor. A adição de um novo administrador da Azure AD do mesmo inquilino irá reativar os logins da Azure AD.
- A Base de Dados Azure para PostgreSQL corresponde a fichas de acesso à Base de Dados Azure para a função PostgreSQL utilizando o ID exclusivo do utilizador Azure AD, em oposição à utilização do nome de utilizador. Isto significa que se um utilizador da AD Azure for eliminado em Azure AD e um novo utilizador criado com o mesmo nome, a Base de Dados Azure para postgreSQL considera que um utilizador diferente. Portanto, se um utilizador for eliminado do Azure AD e, em seguida, um novo utilizador com o mesmo nome adicionado, o novo utilizador não poderá ligar-se à função existente. Para isso, a Base de Dados Azure para administrador aD PostgreSQL Azure deve revogar e, em seguida, conceder o papel "azure_ad_user" ao utilizador para atualizar o ID do utilizador Azure AD.

## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar e povoar o Azure AD, e depois configurar o Azure AD com base de dados Azure para PostgreSQL, consulte [configurar e iniciar sessão com a Azure AD para Azure Database para PostgreSQL](howto-configure-sign-in-aad-authentication.md).
- Para uma visão geral dos papéis de login, utilizadores e bases de dados Azure Database for PostgreSQL, consulte [Create users in Azure Database for PostgreSQL - Single Server](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
