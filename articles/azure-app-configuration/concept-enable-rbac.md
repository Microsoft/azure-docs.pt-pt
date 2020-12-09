---
title: Autorizar o acesso à Configuração de Aplicações Azure utilizando o Azure Ative Directory
description: Enable Azure RBAC para autorizar o acesso à sua instância de Configuração de Aplicação Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9c749dbb1cc78bf06dfe665875bc05f75152778b
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862144"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizar o acesso à Configuração de Aplicações Azure utilizando o Azure Ative Directory
Além de utilizar o Código de Autenticação de Mensagens (HMAC) baseado em Hash, a Azure App Configuration suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos para instâncias de Configuração de Aplicações.  A Azure AD permite-lhe usar o controlo de acesso baseado em funções Azure (Azure RBAC) para conceder permissões a um diretor de segurança.  Um responsável de segurança pode ser um utilizador, uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) ou um diretor de serviço [de aplicação.](../active-directory/develop/app-objects-and-service-principals.md)  Para saber mais sobre papéis e atribuições de papéis, consulte [compreender diferentes funções.](../role-based-access-control/overview.md)

## <a name="overview"></a>Descrição geral
Os pedidos feitos por um responsável de segurança para aceder a um recurso de Configuração de Aplicações devem ser autorizados. Com a Azure AD, o acesso a um recurso é um processo em duas etapas:
1. A identidade do diretor de segurança é autenticada e um token OAuth 2.0 é devolvido.  O nome do recurso para solicitar um token é `https://login.microsoftonline.com/{tenantID}` onde `{tenantID}` corresponde ao ID do inquilino do Azure Ative Directory ao qual o principal de serviço pertence.
2. O token é passado como parte de um pedido ao serviço de Configuração de Aplicações para autorizar o acesso ao recurso especificado.

O passo de autenticação requer que um pedido de candidatura contenha um token de acesso OAuth 2.0 no tempo de execução.  Se uma aplicação estiver a ser executada dentro de uma entidade Azure, como uma aplicação Azure Functions, uma App Azure Web ou um Azure VM, pode usar uma identidade gerida para aceder aos recursos.  Para aprender a autenticar pedidos feitos por uma identidade gerida para a Configuração da Aplicação Azure, consulte [autenticar o acesso aos recursos de configuração de aplicações Azure com o Azure Ative Directory e identidades geridas para recursos Azure.](howto-integrate-azure-managed-service-identity.md)

O passo de autorização requer que uma ou mais funções da Azure sejam atribuídas ao diretor de segurança. A Azure App Configuration fornece funções Azure que englobam conjuntos de permissões para recursos de configuração de aplicações. As funções atribuídas a um diretor de segurança determinam as permissões fornecidas ao diretor. Para obter mais informações sobre os papéis do Azure, consulte [os papéis incorporados do Azure para a Configuração da Aplicação Azure.](#azure-built-in-roles-for-azure-app-configuration) 

## <a name="assign-azure-roles-for-access-rights"></a>Atribuir funções da Azure para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através do [controlo de acesso baseado em funções da Azure (Azure RBAC)](../role-based-access-control/overview.md).

Quando um papel de Azure é atribuído a um diretor de segurança da AZure, a Azure concede acesso a esses recursos para esse diretor de segurança. O acesso é traçado para o recurso de Configuração de Aplicações. Um diretor de segurança Azure AD pode ser um utilizador, ou um diretor de serviço de aplicação, ou uma [identidade gerida para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Azure funções incorporadas para configuração de aplicativos Azure
O Azure fornece as seguintes funções incorporadas do Azure para autorizar o acesso aos dados de Configuração de Aplicações utilizando Azure AD e OAuth:

- **Proprietário de dados de configuração de aplicativos**: Utilize esta função para dar acesso de leitura/escrita/exclusão aos dados de Configuração de Aplicações. Isto não permite o acesso ao recurso de Configuração de Aplicações.
- **Leitor de Dados de Configuração de Aplicativos**: Utilize esta função para dar acesso à leitura dos dados de Configuração da Aplicação. Isto não permite o acesso ao recurso de Configuração de Aplicações.
- **Contribuinte**: Utilize esta função para gerir o recurso de Configuração de Aplicações. Embora os dados de Configuração da Aplicação possam ser acedidos através de teclas de acesso, esta função não concede acesso direto aos dados utilizando o Azure AD.
- **Leitor**: Utilize esta função para dar acesso à leitura do recurso De configuração da aplicação. Isto não permite o acesso às teclas de acesso do recurso, nem aos dados armazenados na Configuração da Aplicação.

> [!NOTE]
> Atualmente, o portal Azure suporta apenas a autenticação HMAC para aceder aos dados de Configuração de Aplicações. A autenticação AD AZure não é suportada. Por isso, os utilizadores do portal Azure exigem que a função *do Contribuinte* recupere as teclas de acesso do recurso de Configuração da Aplicação. A concessão de funções *de Leitor de Dados de Configuração de Aplicações* ou *de Data Owner de Configuração de Aplicações* não tem qualquer impacto no acesso através do portal.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a utilização [de identidades geridas](howto-integrate-azure-managed-service-identity.md) para administrar o seu serviço de Configuração de Aplicações.
