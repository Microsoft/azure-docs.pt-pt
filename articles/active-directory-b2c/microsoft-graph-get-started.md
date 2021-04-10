---
title: Registar uma aplicação do Microsoft Graph
titleSuffix: Azure AD B2C
description: Prepare-se para gerir os recursos Azure AD B2C com o Microsoft Graph registando uma aplicação que concedeu as permissões de API do gráfico necessários.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0dcb959184e12ffa22ae25443087684123598e47
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382471"
---
# <a name="register-a-microsoft-graph-application"></a>Registar uma aplicação do Microsoft Graph

[O Microsoft Graph][ms-graph] permite-lhe gerir muitos dos recursos dentro do seu inquilino Azure AD B2C, incluindo contas de utilizador de clientes e políticas personalizadas. Ao escrever scripts ou aplicações que liguem para a [Microsoft Graph API,][ms-graph-api]pode automatizar tarefas de gestão de inquilinos como:

* Migrar uma loja de utilizadores existente para um inquilino Azure AD B2C
* Implementar políticas personalizadas com um pipeline Azure em Azure DevOps e gerir chaves de política personalizadas
* Hospedar o registo do utilizador na sua própria página e criar contas de utilizador no seu diretório Azure AD B2C nos bastidores
* Automatizar o registo de pedidos
* Obter registos de auditoria

As seguintes secções ajudam-no a preparar-se para a utilização da API do Gráfico microsoft para automatizar a gestão de recursos no seu diretório Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Modos de interação API do Microsoft Graph

Existem dois modos de comunicação que pode utilizar ao trabalhar com a Microsoft Graph API para gerir recursos no seu inquilino Azure AD B2C:

* **Interativo** - Adequado para tarefas de execução, utiliza uma conta de administrador no inquilino B2C para executar as tarefas de gestão. Este modo requer que um administrador inscreva-se usando as suas credenciais antes de ligar para a API do Gráfico microsoft.

* **Automatizado** - Para tarefas programadas ou executadas continuamente, este método utiliza uma conta de serviço que configura com as permissões necessárias para executar tarefas de gestão. Cria a "conta de serviço" em Azure AD B2C registando uma aplicação que as suas aplicações e scripts utilizam para autenticar utilizando o seu *ID de Aplicação (Cliente)* e a concessão de credenciais de **cliente OAuth 2.0.** Neste caso, a aplicação atua como ele próprio para chamar a API do Microsoft Graph, e não o utilizador administrador como no método interativo previamente descrito.

Ativa o cenário de interação **automatizada** criando um registo de candidatura mostrado nas seguintes secções.

Embora o fluxo de concessão de credenciais de cliente OAuth 2.0 não seja suportado diretamente pelo serviço de autenticação Azure AD B2C, pode configurar o fluxo de credencial do cliente utilizando a Azure AD e a plataforma de identidade da Microsoft/ponto final simbólico para uma aplicação no seu inquilino Azure AD B2C. Um inquilino Azure AD B2C partilha alguma funcionalidade com inquilinos da AZure AD.

## <a name="register-management-application"></a>Aplicação de gestão de registos

Antes de os seus scripts e aplicações poderem interagir com a [Microsoft Graph API][ms-graph-api] para gerir os recursos Azure AD B2C, precisa de criar um registo de candidatura no seu inquilino AZure AD B2C que concede as permissões API necessárias.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *o 'managementapp1' ( aplicação de gestão).*
1. Selecione **Contas apenas neste diretório organizacional**.
1. Ao abrigo **de permissões,** limpe o *consentimento da administração Grant para abrir e offline_access caixa de verificação de permissões.*
1. Selecione **Registar**.
1. Grave o **ID da Aplicação (cliente)** que aparece na página geral da aplicação. Usas este valor num passo posterior.

## <a name="grant-api-access"></a>Conceder acesso à API

Para a sua aplicação aceder aos dados no Microsoft Graph, conceda à aplicação registada as permissões de [aplicação relevantes.](https://docs.microsoft.com/graph/permissions-reference) As permissões efetivas da sua aplicação são o nível completo de privilégios implícitos pela permissão. Por exemplo, para *criar,* *ler,* *atualizar* e *eliminar* todos os utilizadores do seu inquilino Azure AD B2C, adicione a permissão **User.ReadWrite.All.** 

> [!NOTE]
> A permissão **User.ReadWrite.All** não inclui as palavras-passe da conta de utilizador de atualização de capacidade. Se a sua aplicação necessitar de atualizar as palavras-passe da conta de utilizador, [conceda a função de administrador do utilizador](#optional-grant-user-administrator-role). Ao conceder a função de administrador do [utilizador,](../active-directory/roles/permissions-reference.md#user-administrator) o **Utilizador.ReadWrite.Todos** não são necessários. A função de administrador do utilizador inclui tudo o que é necessário para gerir os utilizadores.

Pode conceder ao seu pedido múltiplas permissões de candidatura. Por exemplo, se a sua aplicação também precisar de gerir grupos no seu inquilino Azure AD B2C, adicione o **Grupo.ReadWrite.Todas as** permissões também. 

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]


## <a name="optional-grant-user-administrator-role"></a>[Opcional] Papel de administrador de utilizador de subvenção

Se a sua aplicação ou script precisar de atualizar as palavras-passe dos utilizadores, tem de atribuir a *função de administrador* do Utilizador à sua aplicação. A [função de administrador do Utilizador](../active-directory/roles/permissions-reference.md#user-administrator) tem um conjunto fixo de permissões que concede à sua aplicação. 

Para adicionar a *função de administrador do Utilizador,* siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e use o filtro **Diretório + Subscrição** para mudar para o seu inquilino Azure AD B2C.
1. Procure e selecione **Azure AD B2C**.
1. Em **Gestão**, **selecione Funções e administradores**.
1. Selecione a **função de administrador do utilizador.** 
1. Selecione **Adicionar atribuições**.
1. Na caixa de texto **Select,** insira o nome ou o ID da aplicação que registou anteriormente, por exemplo, *o 'managementapp1' ( aplicação de gestão).* Quando aparecer nos resultados da pesquisa, selecione a sua aplicação.
1. Selecione **Adicionar**. Pode levar alguns minutos para que as permissões se propaguem completamente.

## <a name="create-client-secret"></a>Criar segredo de cliente

A sua candidatura precisa de um segredo de cliente para provar a sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estes passos:

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]


## <a name="next-steps"></a>Passos seguintes

Agora que registou o seu pedido de gestão e lhe concedeu as permissões necessárias, as suas aplicações e serviços (por exemplo, Azure Pipelines) podem usar as suas credenciais e permissões para interagir com a API do Microsoft Graph. 

* [Obter um token de acesso do Azure AD](/graph/auth-v2-service#4-get-an-access-token)
* [Use o token de acesso para ligar para o Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [Operações B2C suportadas pelo Microsoft Graph](microsoft-graph-operations.md)
* [Gerir contas de utilizadores AZure AD B2C com Gráfico microsoft](microsoft-graph-operations.md)
* [Obtenha registos de auditoria com a AZure AD reportando API](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
