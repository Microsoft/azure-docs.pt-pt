---
title: Autenticação do Diretório Ativo - Base de Dados Azure para o MySQL
description: Conheça os conceitos do Azure Ative Directory para autenticação com Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 942789d5541e796247af2dbdf9371cce4701105e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87171888"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Utilizar o Azure Ative Directory para a autenticação com o MySQL

A autenticação do Microsoft Azure Ative Directory (Azure AD) é um mecanismo de ligação à Base de Dados Azure para o MySQL utilizando identidades definidas no Azure AD.
Com a autenticação Azure AD, pode gerir identidades de utilizadores de bases de dados e outros serviços da Microsoft numa localização central, o que simplifica a gestão da permissão.

Os benefícios da utilização da AZure AD incluem:

- Autenticação de utilizadores em todos os Serviços Azure de forma uniforme
- Gestão de políticas de senha e rotação de senha num único local
- Múltiplas formas de autenticação suportadas pelo Azure Ative Directory, que podem eliminar a necessidade de armazenar senhas
- Os clientes podem gerir permissões de base de dados utilizando grupos externos (Azure AD).
- A autenticação AZURE AD utiliza utilizadores da base de dados MySQL para autenticar identidades ao nível da base de dados
- Suporte à autenticação baseada em fichas para aplicações que se ligam à Base de Dados Azure para o MySQL

Para configurar e utilizar a autenticação do Azure Ative Directory, utilize o seguinte processo:

1. Crie e povoe o Azure Ative Directory com identidades de utilizador, conforme necessário.
2. Associar opcionalmente ou alterar o Ative Directory atualmente associado à sua subscrição Azure.
3. Crie um administrador AD Azure para a Base de Dados Azure para o servidor MySQL.
4. Crie utilizadores de bases de dados na sua base de dados mapeadas para identidades Azure AD.
5. Conecte-se à sua base de dados recuperando um símbolo para uma identidade AD AZure e iniciando sessão.

> [!NOTE]
> Para aprender a criar e povoar a AD AD azul, e depois configurar a Azure AD com a Azure Database for MySQL, consulte [Configure e inscreva-se com Azure AD para Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Arquitetura

O diagrama de alto nível que se segue resume como funciona a autenticação utilizando a autenticação Azure AD com base de dados Azure para o MySQL. As setas indicam vias de comunicação.

![fluxo de autenticação][1]

## <a name="administrator-structure"></a>Estrutura de administrador

Ao utilizar a autenticação AZure AD, existem duas contas de Administrador para o servidor MySQL; o administrador original do MySQL e o administrador AD da Azure. Apenas o administrador baseado numa conta AZure AD pode criar o primeiro utilizador de base de dados Azure AD contido numa base de dados de utilizador. O login do administrador AZure AD pode ser um utilizador AD Azure ou um grupo AZure AD. Quando o administrador é uma conta de grupo, pode ser utilizado por qualquer membro do grupo, permitindo vários administradores AD Azure para o servidor MySQL. A utilização de uma conta de grupo como administrador aumenta a gestão, permitindo-lhe adicionar e remover centralmente membros do grupo em AZure AD sem alterar os utilizadores ou permissões no servidor MySQL. Apenas um administrador AD AZure (um utilizador ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração][2]

## <a name="permissions"></a>Permissões

Para criar novos utilizadores que possam autenticar com Azure AD, tem de ser o administrador AD AZure projetado. Este utilizador é atribuído configurando a conta administrador ad Azure para uma base de dados Azure específica para o servidor MySQL.

Para criar um novo utilizador de base de dados Azure AD, tem de se ligar como administrador AD Azure. Isto é demonstrado em [Configure e Login com Azure AD para Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

Qualquer autenticação AZure AD só é possível se o administrador AD AZure tiver sido criado para a Base de Dados Azure para o MySQL. Se o administrador do Azure Ative Directory foi removido do servidor, os utilizadores existentes do Azure Ative Directory criados anteriormente já não podem ligar-se à base de dados utilizando as suas credenciais de Diretório Ativo Azure.

## <a name="connecting-using-azure-ad-identities"></a>Ligação utilizando identidades Azure AD

A autenticação do Azure Ative Directory suporta os seguintes métodos de ligação a uma base de dados utilizando identidades Azure AD:

- Senha de diretório ativo Azure
- Diretório Ativo Azure Integrado
- Diretório Ativo Azure Universal com MFA
- Utilização de certificados de aplicação de diretório ativo ou segredos de cliente
- [Identidade Gerida](howto-connect-with-managed-identity.md)

Depois de autenticar contra o Diretório Ativo, então recupere um símbolo. Este token é a sua senha para iniciar sessão.

Por favor, note que as operações de gestão, como a adição de novos utilizadores, só são suportadas para as funções de utilizador da Azure AD neste momento.

> [!NOTE]
> Para obter mais detalhes sobre como ligar-se a um token ative directory, consulte [Configure e inscreva-se com a Azure AD para a Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Considerações adicionais

- A autenticação do Azure Ative Directory só está disponível para o MySQL 5.7 e mais recente.
- Apenas um administrador AD AZure pode ser configurado para uma Base de Dados Azure para o servidor MySQL a qualquer momento.
- Apenas um administrador AD AD do MySQL pode inicialmente ligar-se à Base de Dados Azure para o MySQL utilizando uma conta do Azure Ative Directory. O administrador ative directory pode configurar os utilizadores subsequentes da base de dados Azure AD.
- Se um utilizador for eliminado do Azure AD, esse utilizador deixará de poder autenticar-se com a Azure AD, pelo que deixará de ser possível adquirir um token de acesso para esse utilizador. Neste caso, apesar de o utilizador correspondente ainda se encontrar na base de dados, não será possível ligar-se ao servidor com esse utilizador.
> [!NOTE]
> O login com o utilizador AZure AD eliminado ainda pode ser feito até que o token expire (até 60 minutos a partir da emissão de token).  Se também remover o utilizador da Base de Dados Azure para o MySQL, este acesso será revogado imediatamente.
- Se o administrador AD AD do Azure for removido do servidor, o servidor deixará de estar associado a um inquilino AZure AD, pelo que todos os logins AD do Azure serão desativados para o servidor. A adição de um novo administrador AD AD do mesmo inquilino irá ree capacitar os logins AZure AD.
- A Azure Database for MySQL corresponde aos tokens de acesso à Base de Dados Azure para o utilizador MySQL utilizando o ID único do utilizador Azure AD, em oposição à utilização do nome de utilizador. Isto significa que se um utilizador AD Azure for eliminado em Azure AD e um novo utilizador criado com o mesmo nome, a Azure Database para o MySQL considera que é um utilizador diferente. Portanto, se um utilizador for eliminado do Azure AD e, em seguida, um novo utilizador com o mesmo nome adicionado, o novo utilizador não poderá ligar-se ao utilizador existente.

## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar e povoar a AD AD azul, e depois configurar a Azure AD com a Azure Database for MySQL, consulte [Configure e inscreva-se com Azure AD para Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).
- Para obter uma visão geral dos logins e dos utilizadores de bases de dados para Azure Database for MySQL, consulte [criar utilizadores na Base de Dados Azure para o MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
