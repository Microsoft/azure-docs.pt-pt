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
ms.date: 01/21/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67870a458138101f3b8a009f7c96c74991396284
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675191"
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

### <a name="grant-api-access"></a>Conceder acesso à API

Em seguida, conceda as permissões de aplicação registadas para manipular os recursos dos inquilinos através de chamadas para a Microsoft Graph API.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Criar segredo de cliente

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Tem agora uma aplicação que tem permissão para *criar,* *ler,* *atualizar* e *eliminar* utilizadores no seu inquilino Azure AD B2C. Continue até a secção seguinte para adicionar permissões *de atualização de palavra-passe.*

## <a name="enable-user-delete-and-password-update"></a>Ativar a eliminação do utilizador e atualização de palavra-passe

A permissão *de dados de diretório de leitura e escrita* **NÃO** inclui a capacidade de eliminar utilizadores ou atualizar palavras-passe da conta de utilizador.

Se a sua aplicação ou script precisar de eliminar utilizadores ou atualizar as suas palavras-passe, atribua a *função de administrador do Utilizador* à sua aplicação:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e use o filtro **Diretório + Subscrição** para mudar para o seu inquilino Azure AD B2C.
1. Procure e selecione **Azure AD B2C**.
1. Em **Gestão**, **selecione Funções e administradores**.
1. Selecione a **função de administrador do utilizador.**
1. Selecione **Adicionar atribuições**.
1. Na caixa de texto **Select,** insira o nome da aplicação que registou anteriormente, por exemplo, *o 'managementapp1' ( aplicação).* Selecione a sua aplicação quando aparecer nos resultados da pesquisa.
1. Selecione **Adicionar**. Pode levar alguns minutos para que as permissões se propaguem completamente.

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
