---
title: Gerir recursos com o Microsoft Graph
titleSuffix: Azure AD B2C
description: Prepare-se para gerir os recursos Do Azure AD B2C com o Microsoft Graph, registando uma aplicação que tenha concedido as permissões API de Gráfico saneadas.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184362"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Gerir o Azure AD B2C com o Microsoft Graph

O [Microsoft Graph][ms-graph] permite-lhe gerir muitos dos recursos dentro do seu inquilino Azure AD B2C, incluindo contas de utilizadores de clientes e políticas personalizadas. Ao escrever scripts ou aplicações que liguem para a [Microsoft Graph API,][ms-graph-api]pode automatizar tarefas de gestão de inquilinos como:

* Migrar uma loja de utilizadores existente para um inquilino Azure AD B2C
* Implementar políticas personalizadas com um Pipeline Azure em Azure DevOps, e gerir chaves políticas personalizadas
* Registo de utilizador na sua própria página e crie contas de utilizador no seu diretório Azure AD B2C nos bastidores
* Registo de candidatura automatizada
* Obtenha registos de auditoria

As seguintes secções ajudam-no a preparar-se para utilizar a API do Microsoft Graph para automatizar a gestão de recursos no seu diretório Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Modos de interação Microsoft Graph API

Existem dois modos de comunicação que pode utilizar ao trabalhar com a API do Microsoft Graph para gerir recursos no seu inquilino Azure AD B2C:

* **Interativo** - Adequado para tarefas executadas, utiliza uma conta de administrador no inquilino B2C para executar as tarefas de gestão. Este modo requer que um administrador assine as suas credenciais antes de ligar para a Microsoft Graph API.

* **Automatizado** - Para tarefas programadas ou de execução contínua, este método utiliza uma conta de serviço que configura com as permissões necessárias para executar tarefas de gestão. Cria a "conta de serviço" no Azure AD B2C registando uma aplicação que as suas aplicações e scripts utilizam para autenticar utilizando o seu ID de *Aplicação (Cliente)* e a concessão de credenciais de clientes OAuth 2.0. Neste caso, a aplicação atua como se fosse para chamar a API do Microsoft Graph, e não o utilizador administrador como no método interativo previamente descrito.

Habilita o cenário de interação **Automatizado** criando um registo de aplicação mostrado nas seguintes secções.

## <a name="register-management-application"></a>Pedido de gestão de registos

Antes que os seus scripts e aplicações possam interagir com a [API][ms-graph-api] do Microsoft Graph para gerir os recursos do Azure AD B2C, precisa de criar um registo de aplicação no seu inquilino Azure AD AD B2C que conceda as permissões API necessárias.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>Conceder acesso à API

Em seguida, conceda as permissões de aplicação registadas para manipular os recursos dos inquilinos através de chamadas para a API do Microsoft Graph.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Criar o segredo do cliente

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Tem agora uma aplicação que tem permissão para *criar,* *ler,* *atualizar*e *eliminar* utilizadores no seu inquilino Azure AD AD B2C. Continue na secção seguinte para adicionar permissões de atualização de *palavra-passe.*

## <a name="enable-user-delete-and-password-update"></a>Ativar a eliminação do utilizador e atualização de palavra-passe

A permissão de dados de *leitura e escrita* **não** inclui a capacidade de eliminar utilizadores ou atualizar as palavras-passe da conta do utilizador.

Se a sua aplicação ou script necessitar de excluir utilizadores ou atualizar as suas palavras-passe, atribua a função *de administrador* do Utilizador à sua aplicação:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e utilize o filtro **de assinatura Diretório + Para** mudar para o seu inquilino Azure AD AD B2C.
1. Procure e **selecione Azure AD B2C**.
1. Em **Gerir,** selecione **Funções e administradores.**
1. Selecione a função de administrador do **Utilizador.**
1. Selecione **Adicionar atribuições**.
1. Na caixa de texto **Select,** insira o nome da aplicação que registou anteriormente, por exemplo, *o managementapp1*. Selecione a sua aplicação quando aparecer nos resultados da pesquisa.
1. Selecione **Adicionar**. Pode levar alguns minutos para as permissões se propagarem completamente.

## <a name="next-steps"></a>Passos seguintes

Agora que registou o seu pedido de gestão e lhe concedeu as permissões necessárias, as suas aplicações e serviços (por exemplo, Pipelines Azure) podem usar as suas credenciais e permissões para interagir com a API do Microsoft Graph.

* [Operações B2C suportadas pelo Microsoft Graph](microsoft-graph-operations.md)
* [Gerir contas de utilizadores do Azure AD B2C com o Microsoft Graph](manage-user-accounts-graph-api.md)
* [Obtenha registos de auditoria com a AD Azure reportando API](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
