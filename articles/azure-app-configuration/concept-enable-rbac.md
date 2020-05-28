---
title: Autorizar acesso à Configuração de Aplicações Azure utilizando o Diretório Ativo do Azure
description: Ativar o RBAC para autorizar o acesso à sua instância de configuração de aplicações Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: c2812219e689cb42fd871f85300239a10ab0da0e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116711"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizar acesso à Configuração de Aplicações Azure utilizando o Diretório Ativo do Azure
Além de utilizar o Código de Autenticação de Mensagens (HMAC) baseado em Hash, a Configuração de Aplicações Azure suporta a utilização do Diretório Ativo Azure (Azure AD) para autorizar pedidos para casos de Configuração de Aplicações.  A Azure AD permite-lhe utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um diretor de segurança.  Um diretor de segurança pode ser um utilizador, uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) ou um diretor de serviço de [aplicação.](../active-directory/develop/app-objects-and-service-principals.md)  Para saber mais sobre papéis e atribuições de papéis, consulte [compreender diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Descrição geral
Os pedidos feitos por um diretor de segurança para aceder a um recurso de Configuração de Aplicações devem ser autorizados. Com a AD Azure, o acesso a um recurso é um processo em duas etapas:
1. A identidade do diretor de segurança é autenticada e um token OAuth 2.0 é devolvido.  O nome do recurso para solicitar um símbolo é onde corresponde ao ID de inquilino do `https://login.microsoftonline.com/{tenantID}` `{tenantID}` Azure Ative Directory a que pertence o diretor de serviço.
2. O símbolo é passado como parte de um pedido ao serviço de Configuração de Aplicações para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um pedido de pedido contenha um sinal de acesso OAuth 2.0 no prazo de execução.  Se uma aplicação estiver a funcionar dentro de uma entidade Azure, como uma aplicação Azure Functions, uma Web App Azure ou um Azure VM, pode usar uma identidade gerida para aceder aos recursos.  Para saber autenticar pedidos feitos por uma identidade gerida para a Configuração de Aplicações Azure, consulte o acesso autenticado aos recursos de configuração de [aplicações do Azure com o Diretório Ativo Azure e identidades geridas para os Recursos Azure.](howto-integrate-azure-managed-service-identity.md)

O passo de autorização requer que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. A Configuração de Aplicações Azure fornece funções RBAC que englobam conjuntos de permissões para recursos de configuração de aplicações. As funções atribuídas a um diretor de segurança determinam as permissões fornecidas ao diretor. Para obter mais informações sobre as funções RBAC, consulte [as funções RBAC incorporadas para configuração de aplicações azure](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções rBAC para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos garantidos através do [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md).

Quando uma função RBAC é atribuída a um diretor de segurança da AD Azure, o Azure concede acesso a esses recursos para esse diretor de segurança. O acesso é acessível ao recurso de Configuração de Aplicações. Um diretor de segurança da AD Azure pode ser um utilizador, ou um diretor de serviço de aplicação, ou uma [identidade gerida para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Funções RBAC incorporadas para configuração de aplicações azure
O Azure fornece as seguintes funções de RBAC incorporadopara autorizar o acesso aos dados de configuração de aplicações utilizando a Azure AD e a OAuth:

- **Configuração de aplicativos Data Owner**: Use esta função para dar acesso a dados de configuração de aplicações/ leitura/escrita/exclusão. Isto não permite o acesso ao recurso de Configuração de Aplicações.
- Leitor de dados de **configuração de aplicações**: Utilize esta função para dar acesso à leitura aos dados de configuração da aplicação. Isto não permite o acesso ao recurso de Configuração de Aplicações.
- **Colaborador**: Utilize esta função para gerir o recurso de configuração da aplicação. Embora os dados de Configuração da Aplicação possam ser acedidos através de chaves de acesso, esta função não permite acesso direto aos dados através do Azure AD.
- **Leitor**: Utilize esta função para dar acesso ao recurso de configuração da aplicação. Isto não permite o acesso às chaves de acesso do recurso, nem aos dados armazenados na Configuração da Aplicação.

> [!NOTE]
> Atualmente, o portal Azure e o CLI apenas suportam a autenticação HMAC para aceder aos dados de Configuração de Aplicações. A autenticação da AD Azure não é suportada. Por isso, os utilizadores do portal Azure e do CLI exigem que o papel *do Contribuinte* recupere as chaves de acesso do recurso de Configuração de Aplicações. A concessão de funções de *Data Reader* ou App Configuration Data Reader ou *App Configuration Data Owner* não tem qualquer impacto no acesso através do portal e do CLI.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre o uso [de identidades geridas](howto-integrate-azure-managed-service-identity.md) para administrar o seu serviço de Configuração de Aplicações.
