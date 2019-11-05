---
title: Autenticação Azure Active Directory com o banco de dados do Azure para PostgreSQL-servidor único
description: Saiba mais sobre os conceitos de Azure Active Directory para autenticação com o banco de dados do Azure para PostgreSQL-servidor único
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 47637396581beeafb0748066cd6a66f011e8eaa1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518739"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Usar Azure Active Directory para autenticação com PostgreSQL

A autenticação do Microsoft Azure Active Directory (AD do Azure) é um mecanismo de conexão ao banco de dados do Azure para PostgreSQL usando identidades definidas no Azure AD.
Com a autenticação do Azure AD, você pode gerenciar identidades de usuário de banco de dados e outros serviços da Microsoft em um local central, o que simplifica o gerenciamento de permissões.

> [!IMPORTANT]
> A autenticação do Azure AD para o banco de dados do Azure para PostgreSQL está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os benefícios de usar o Azure AD incluem:

- Autenticação de usuários em serviços do Azure de forma uniforme
- Gerenciamento de políticas de senha e rotação de senha em um único local
- Várias formas de autenticação com suporte pelo Azure Active Directory, o que pode eliminar a necessidade de armazenar senhas
- Os clientes podem gerenciar permissões de banco de dados usando grupos externos (AD do Azure).
- A autenticação do Azure AD usa funções de banco de dados PostgreSQL para autenticar identidades no nível do banco de dados
- Suporte à autenticação baseada em token para aplicativos que se conectam ao banco de dados do Azure para PostgreSQL

Para configurar e usar a autenticação Azure Active Directory, use o seguinte processo:

1. Crie e popule Azure Active Directory com identidades de usuário, conforme necessário.
2. Opcionalmente, associe ou altere os Active Directory associados atualmente à sua assinatura do Azure.
3. Crie um administrador do Azure AD para o servidor do banco de dados do Azure para PostgreSQL.
4. Crie usuários de banco de dados em seu banco de dados mapeado para identidades do Azure AD.
5. Conecte-se ao banco de dados recuperando um token para uma identidade do Azure AD e fazendo logon.

> [!NOTE]
> Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o banco de dados do Azure para PostgreSQL, consulte [configurar e entrar com o Azure ad para o banco de dados do Azure para PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="architecture"></a>Arquitetura

O diagrama de alto nível a seguir resume como a autenticação funciona usando a autenticação do Azure AD com o banco de dados do Azure para PostgreSQL. As setas indicam os caminhos de comunicação.

![Fluxo de autenticação][1]

## <a name="administrator-structure"></a>Estrutura do administrador

Ao usar a autenticação do Azure AD, há duas contas de administrador para o servidor PostgreSQL; o administrador do PostgreSQL original e o administrador do Azure AD. Somente o administrador com base em uma conta do Azure AD pode criar o primeiro usuário de banco de dados independente do Azure AD em um banco de dados de usuário. O logon de administrador do Azure AD pode ser um usuário do Azure AD ou um grupo do Azure AD. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, permitindo vários administradores do Azure AD para o servidor PostgreSQL. O uso de uma conta de grupo como administrador aprimora a capacidade de gerenciamento, permitindo adicionar e remover centralmente membros do grupo no Azure AD sem alterar os usuários ou permissões no servidor PostgreSQL. Somente um administrador do Azure AD (um usuário ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração][2]

## <a name="permissions"></a>Permissões

Para criar novos usuários que podem se autenticar com o Azure AD, você deve ter a função `azure_ad_admin` no banco de dados. Essa função é atribuída Configurando a conta de administrador do Azure AD para um servidor de banco de dados do Azure específico para PostgreSQL.

Para criar um novo usuário de banco de dados do Azure AD, você deve se conectar como administrador do Azure AD. Isso é demonstrado em [configurar e fazer logon com o Azure ad para o banco de dados do Azure para PostgreSQL](howto-configure-sign-in-aad-authentication.md).

Qualquer autenticação do Azure AD só será possível se o administrador do Azure AD tiver sido criado para o banco de dados do Azure para PostgreSQL. Se o administrador do Azure Active Directory tiver sido removido do servidor, Azure Active Directory usuários existentes criados anteriormente não poderão mais se conectar ao banco de dados usando suas credenciais de Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Conectando-se usando identidades do Azure AD

Azure Active Directory autenticação dá suporte aos seguintes métodos de conexão a um banco de dados usando identidades do Azure AD:

- Azure Active Directory senha
- Azure Active Directory integrado
- Azure Active Directory universal com MFA
- Usando Active Directory certificados de aplicativo ou segredos de cliente

Depois de autenticado no Active Directory, você recupera um token. Esse token é sua senha para fazer logon.

> [!NOTE]
> Para obter mais detalhes sobre como se conectar com um token de Active Directory, consulte [configurar e entrar com o Azure ad para o banco de dados do Azure para PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a capacidade de gerenciamento, recomendamos que você provisione um grupo dedicado do Azure AD como administrador.
- Somente um administrador do Azure AD (um usuário ou grupo) pode ser configurado para um servidor de banco de dados do Azure para PostgreSQL a qualquer momento.
- Somente um administrador do Azure AD para PostgreSQL pode se conectar inicialmente ao banco de dados do Azure para PostgreSQL usando uma conta de Azure Active Directory. O administrador de Active Directory pode configurar os usuários de banco de dados do Azure AD subsequentes.
- Se um usuário for excluído do Azure AD, esse usuário não poderá mais se autenticar com o Azure AD e, portanto, não será mais possível adquirir um token de acesso para esse usuário. Nesse caso, embora a função correspondente ainda esteja no banco de dados, não será possível se conectar ao servidor com essa função.
> [!NOTE]
> O logon com o usuário do Azure AD excluído ainda pode ser feito até que o token expire (até 60 minutos de emissão de tokens).  Se você também remover o usuário do banco de dados do Azure para PostgreSQL, esse acesso será revogado imediatamente.
- Se o administrador do Azure AD for removido do servidor, o servidor não será mais associado a um locatário do Azure AD e, portanto, todos os logons do Azure AD serão desabilitados para o servidor. Adicionar um novo administrador do Azure AD do mesmo locatário reativará logons do Azure AD.
- O banco de dados do Azure para PostgreSQL corresponde aos tokens de acesso para a função de banco de dados do Azure para PostgreSQL usando a ID de usuário exclusiva do Azure AD do usuário, em oposição ao uso de username. Isso significa que, se um usuário do Azure AD for excluído do Azure AD e um novo usuário criado com o mesmo nome, o banco de dados do Azure para PostgreSQL considerará que um usuário diferente. Portanto, se um usuário for excluído do Azure AD e um novo usuário com o mesmo nome for adicionado, o novo usuário não poderá se conectar com a função existente. Para permitir isso, o administrador do banco de dados do Azure para PostgreSQL do Azure AD deve revogar e, em seguida, conceder a função "azure_ad_user" ao usuário para atualizar a ID de usuário do Azure AD.

## <a name="next-steps"></a>Passos seguintes

- Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o banco de dados do Azure para PostgreSQL, consulte [configurar e entrar com o Azure ad para o banco de dados do Azure para PostgreSQL](howto-configure-sign-in-aad-authentication.md).
- Para obter uma visão geral de logons, usuários e funções de banco de dados do Azure para PostgreSQL, consulte [criar usuários no banco de dados do Azure para PostgreSQL-servidor único](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
