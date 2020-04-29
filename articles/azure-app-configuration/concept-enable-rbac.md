---
title: Autorizar acesso à Configuração de Aplicações Azure utilizando o Diretório Ativo do Azure
description: Ativar o RBAC para autorizar o acesso à sua instância de configuração de aplicações Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77472625"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizar acesso à Configuração de Aplicações Azure utilizando o Diretório Ativo do Azure
A Configuração de Aplicações Azure suporta a utilização do Diretório Ativo Azure (Azure AD) para autorizar pedidos para casos de Configuração de Aplicações.  A Azure AD permite-lhe utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um diretor de segurança.  Um diretor de segurança pode ser um utilizador, ou um diretor de serviço de [aplicação.](../active-directory/develop/app-objects-and-service-principals.md)  Para saber mais sobre papéis e atribuições de papéis, consulte [compreender diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Descrição geral
Os pedidos feitos pelo diretor de segurança (um utilizador ou uma aplicação) para aceder a um recurso de Configuração de Aplicações devem ser autorizados.  Com a AD Azure, o acesso a um recurso é um processo em duas etapas.
1. A identidade do diretor de segurança é autenticada e um token OAuth 2.0 é devolvido.  O nome do recurso para `https://login.microsoftonline.com/{tenantID}` `{tenantID}` solicitar um símbolo é onde corresponde ao ID de inquilino do Azure Ative Directory a que pertence o diretor de serviço.
2. O símbolo é passado como parte de um pedido ao serviço de Configuração de Aplicações para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um pedido de pedido contenha um sinal de acesso OAuth 2.0 no prazo de execução.  Se uma aplicação estiver a funcionar dentro de uma entidade Azure, como uma aplicação Azure Functions, uma Web App Azure ou um Azure VM, pode usar uma identidade gerida para aceder aos recursos.  Para saber autenticar pedidos feitos por uma identidade gerida para a Configuração de Aplicações Azure, consulte o acesso autenticado aos recursos de configuração de [aplicações do Azure com o Diretório Ativo Azure e identidades geridas para os Recursos Azure.](howto-integrate-azure-managed-service-identity.md)

O passo de autorização requer que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. A Configuração de Aplicações Azure fornece funções RBAC que englobam conjuntos de permissões para recursos de configuração de aplicações. As funções atribuídas a um diretor de segurança determinam as permissões fornecidas ao diretor. Para obter mais informações sobre as funções RBAC, consulte [as funções RBAC incorporadas para configuração de aplicações azure](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções rBAC para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos garantidos através do [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md).

Quando uma função RBAC é atribuída a um diretor de segurança da AD Azure, o Azure concede acesso a esses recursos para esse diretor de segurança. O acesso é acessível ao recurso de Configuração de Aplicações. Um diretor de segurança da AD Azure pode ser um utilizador, ou um diretor de serviço de aplicação, ou uma [identidade gerida para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Funções RBAC incorporadas para configuração de aplicações azure
O Azure fornece as seguintes funções de RBAC incorporadopara autorizar o acesso aos dados de configuração de aplicações utilizando a Azure AD e a OAuth:

- Dono de dados de configuração de aplicações do Azure: Use esta função para dar acesso a leitura/escrita aos recursos de configuração da aplicação.
- Leitor de dados de configuração de aplicações azure: Use esta função para dar acesso a recursos de configuração de aplicações.
- Colaborador: Utilize esta função para dar acesso ao serviço sem conceder acesso aos dados armazenados na instância de Configuração da Aplicação.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o uso [de identidades geridas](howto-integrate-azure-managed-service-identity.md) para administrar o seu serviço de Configuração de Aplicações.