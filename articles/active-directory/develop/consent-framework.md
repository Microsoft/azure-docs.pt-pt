---
title: Quadro de consentimento Azure AD
titleSuffix: Microsoft identity platform
description: Conheça o quadro de consentimento no Azure Ative Directory e como facilita o desenvolvimento de aplicações de clientes multi-inquilinos e clientes nativos.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: e9780332ad6279deef63910c7e6ba95e1ccf43bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706137"
---
# <a name="azure-active-directory-consent-framework"></a>Enquadramento do consentimento do Azure Active Directory

O quadro de consentimento do Azure Ative Directory (Azure AD) facilita o desenvolvimento de aplicações de clientes multi-inquilinos e clientes nativos. Estas aplicações permitem a inscrição por conta de utilizador de um inquilino AZure AD que é diferente daquele em que a aplicação está registada. Podem também ter de aceder a APIs web, como a API do Microsoft Graph (para aceder a Azure AD, Intune e serviços na Microsoft 365) e a APIs de outros serviços da Microsoft, além das suas próprias APIs web.

O quadro baseia-se num utilizador ou administrador que dá consentimento a uma aplicação que pede para ser registada no seu diretório, o que pode implicar o acesso aos dados do diretório. Por exemplo, se uma aplicação de cliente web precisar de ler informações de calendário sobre o utilizador a partir do Microsoft 365, esse utilizador é obrigado a consentir primeiro com a aplicação do cliente. Após o consentimento ser dado, a aplicação do cliente poderá ligar para a API do Microsoft Graph em nome do utilizador e utilizar as informações do calendário conforme necessário. A [API do Microsoft Graph](https://developer.microsoft.com/graph) fornece acesso a dados no Microsoft 365 (como calendários e mensagens de Exchange, sites e listas do SharePoint, documentos do OneDrive, cadernos do OneNote, tarefas do Planner e livros de trabalho do Excel), bem como utilizadores e grupos de Azure AD e outros objetos de dados de mais serviços na nuvem da Microsoft.

O quadro de consentimento baseia-se na OAuth 2.0 e nos seus diversos fluxos, tais como concessão de código de autorização e concessão de credenciais de cliente, utilizando clientes públicos ou confidenciais. Ao utilizar o OAuth 2.0, o AZure AD permite construir muitos tipos diferentes de aplicações de clientes - como num telefone, tablet, servidor ou uma aplicação web -- e ter acesso aos recursos necessários.

Para obter mais informações sobre a utilização do quadro de consentimento com bolsas de autorização OAuth2.0, consulte [o acesso autorizado a aplicações web utilizando cenários de AD e Azure AD e](v2-oauth2-auth-code-flow.md) [autenticação para Azure AD](./authentication-vs-authorization.md). Para obter informações sobre o acesso autorizado ao Microsoft 365 através do Microsoft Graph, consulte [a autenticação da App com o Microsoft Graph](/graph/).

## <a name="consent-experience---an-example"></a>Experiência de consentimento - um exemplo

Os passos a seguir mostram-lhe como funciona a experiência de consentimento tanto para o desenvolvedor de aplicações como para o utilizador.

1. Assuma que tem uma aplicação de cliente web que precisa solicitar permissões específicas para aceder a um recurso/API. Você vai aprender a fazer esta configuração na secção seguinte, mas essencialmente o portal Azure é usado para declarar pedidos de permissão no momento da configuração. Tal como outras definições de configuração, tornam-se parte do registo AZure AD da aplicação:

    ![Permissões para outras aplicações](./media/consent-framework/permissions.png)

1. Considere que as permissões da sua aplicação foram atualizadas, a aplicação está em execução, e um utilizador está prestes a usá-la pela primeira vez. Em primeiro lugar, o pedido precisa de obter um código de autorização do ponto final da Azure `/authorize` AD. O código de autorização pode então ser usado para adquirir um novo acesso e atualização token.

1. Se o utilizador ainda não estiver autenticado, o ponto final do Azure AD `/authorize` solicita ao utilizador que entre.

    ![Utilizador ou administrador inscreva-se no Azure AD](./media/consent-framework/usersignin.png)

1. Depois de o utilizador ter assinado, o Azure AD determinará se o utilizador precisa de ser mostrado uma página de consentimento. Esta determinação baseia-se no facto de o utilizador (ou administrador da sua organização) já ter concedido o consentimento do pedido. Se o consentimento ainda não tiver sido concedido, a Azure AD solicita o consentimento do utilizador e exibe as permissões necessárias para funcionar. O conjunto de permissões que são apresentadas no diálogo de consentimento correspondem às selecionadas nas **permissões delegadas** no portal Azure.

    ![Mostra um exemplo de permissões exibidas no diálogo de consentimento](./media/consent-framework/consent.png)

1. Após o consentimento do utilizador, um código de autorização é devolvido à sua aplicação, que é resgatada para adquirir um token de acesso e atualização. Para obter mais informações sobre este fluxo, consulte [o fluxo de código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

1. Como administrador, também pode consentir com as permissões delegadas de uma aplicação em nome de todos os utilizadores do seu inquilino. O consentimento administrativo impede que o diálogo de consentimento apareça para cada utilizador do arrendatário, e pode ser feito no [portal Azure](https://portal.azure.com) pelos utilizadores com a função de administrador. Para saber quais as funções de administrador que podem consentir com permissões delegadas, consulte [permissões de função do Administrador em Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Para consentir com as permissões delegadas de uma aplicação**

   1. Aceda à página de permissões da **API** para a sua aplicação
   1. Clique no botão **de consentimento de administração Grant.**

      ![Conceder permissões para consentimento de administração explícita](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > A concessão de consentimento explícito utilizando o botão **de permissões Grant** é atualmente necessária para aplicações de uma página única (SPA) que usam ADAL.js. Caso contrário, a aplicação falha quando o token de acesso é solicitado.

## <a name="next-steps"></a>Passos seguintes

* Veja [como converter uma app para ser multi-inquilino](howto-convert-app-to-be-multi-tenant.md)
* Para obter mais profundidade, saiba [como o consentimento é suportado na camada do protocolo OAuth 2.0 durante o fluxo de concessão de código de autorização.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)