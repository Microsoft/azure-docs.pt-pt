---
title: Quadro de consentimento da AD Azure
titleSuffix: Microsoft identity platform
description: Conheça o quadro de consentimento no Azure Ative Directory e como facilita o desenvolvimento de aplicações de clientes web e clientes nativos multi-inquilinos.
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
ms.custom: aaddev
ms.openlocfilehash: cb9441e6ce19094ff72e902cdeea151041ceb963
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161149"
---
# <a name="azure-active-directory-consent-framework"></a>Quadro de consentimento do Diretório Ativo Azure

O quadro de consentimento do Azure Ative Directory (Azure AD) facilita o desenvolvimento de aplicações de clientes web e nativos multi-inquilinos. Estas aplicações permitem o início por contas de utilizador de um inquilino da AD Azure que é diferente daquele em que a aplicação está registada. Podem também ter de aceder a APIs web, como a Microsoft Graph API (para aceder a AD, Intune e serviços no Office 365) e outras APIs dos serviços da Microsoft, além das apis da sua própria web.

O quadro baseia-se num utilizador ou num administrador que dá consentimento a uma aplicação que pede para ser registada no seu diretório, o que pode implicar o acesso a dados de diretório. Por exemplo, se uma aplicação de cliente web precisar de ler informações de calendário sobre o utilizador do Office 365, esse utilizador é obrigado a consentir primeiro com a aplicação do cliente. Após o consentimento, a aplicação do cliente poderá ligar para a Microsoft Graph API em nome do utilizador e utilizar as informações do calendário conforme necessário. O [Microsoft Graph API](https://developer.microsoft.com/graph) fornece acesso a dados no Office 365 (como calendários e mensagens do Exchange, sites e listas do SharePoint, documentos do OneDrive, cadernos do OneNote, tarefas do Planner e livros de trabalho do Excel), bem como utilizadores e grupos de AD Azure e outros objetos de dados de mais serviços na nuvem da Microsoft.

O quadro de consentimento baseia-se na OAuth 2.0 e nos seus diversos fluxos, tais como a concessão de código de autorização e a concessão de credenciais de clientes, utilizando clientes públicos ou confidenciais. Ao utilizar o OAuth 2.0, o Azure AD permite construir muitos tipos diferentes de aplicações de clientes - como num telefone, tablet, servidor ou uma aplicação web -- e ter acesso aos recursos necessários.

Para obter mais informações sobre a utilização do quadro de consentimento com as bolsas de autorização OAuth2.0, consulte [Autorizar o acesso a aplicações web utilizando cenários OAuth 2.0 e Azure AD](v2-oauth2-auth-code-flow.md) e [Autenticação para AD Azure](authentication-scenarios.md). Para obter informações sobre o acesso autorizado ao Office 365 através do Microsoft Graph, consulte a [autenticação da Aplicação com](https://developer.microsoft.com/graph/docs/authorization/auth_overview)o Microsoft Graph .

## <a name="consent-experience---an-example"></a>Experiência de consentimento - um exemplo

Os seguintes passos mostram-lhe como funciona a experiência de consentimento tanto para o desenvolvedor da aplicação como para o utilizador.

1. Assuma que tem uma aplicação de cliente web que precisa de solicitar permissões específicas para aceder a um recurso/API. Você vai aprender a fazer esta configuração na secção seguinte, mas essencialmente o portal Azure é usado para declarar pedidos de permissão no momento da configuração. À semelhança de outras configurações de configuração, tornam-se parte do registo da AD Azure da aplicação:

    ![Permissões a outras aplicações](./media/consent-framework/permissions.png)

1. Considere que as permissões da sua aplicação foram atualizadas, a aplicação está em execução e um utilizador está prestes a usá-lo pela primeira vez. Em primeiro lugar, o pedido precisa de obter um código de autorização do ponto final `/authorize` da Azure AD. O código de autorização pode então ser utilizado para adquirir um novo acesso e um sinal de atualização.

1. Se o utilizador ainda não estiver autenticado, o ponto final `/authorize` da Azure AD leva o utilizador a iniciar sessão.

    ![Utilizador ou administrador iniciar sessão na Azure AD](./media/consent-framework/usersignin.png)

1. Depois de o utilizador ter assinado o contrato, o Azure AD determinará se o utilizador precisa de ser mostrado uma página de consentimento. Esta determinação baseia-se no facto de o utilizador (ou administrador da sua organização) já ter concedido o consentimento da aplicação. Se o consentimento ainda não tiver sido concedido, a AD Azure solicita ao utilizador o consentimento e apresenta as permissões necessárias para funcionar. O conjunto de permissões que são apresentadas no diálogo de consentimento coincidecom as selecionadas nas **permissões delegadas** no portal Azure.

    ![Mostra um exemplo de permissões exibidas no diálogo de consentimento](./media/consent-framework/consent.png)

1. Após o consentimento do utilizador, um código de autorização é devolvido à sua aplicação, que é reembolsada para adquirir um token de acesso e um token de atualização. Para obter mais informações sobre este fluxo, consulte o fluxo de código de [autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

1. Como administrador, também pode consentir com as permissões delegadas de uma aplicação em nome de todos os utilizadores do seu inquilino. O consentimento administrativo impede que o diálogo de consentimento apareça para todos os utilizadores do inquilino, podendo ser feito no [portal Azure](https://portal.azure.com) pelos utilizadores com a função de administrador. Para saber quais as funções de administrador que podem consentir com permissões delegadas, consulte [permissões de funções do Administrador em Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Para consentir com as permissões delegadas de uma aplicação**

   1. Vá à página de **permissões da API** para a sua aplicação
   1. Clique no botão de consentimento do **administrador Grant.**

      ![Conceder permissões para consentimento explícito da administração](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > A concessão de um consentimento explícito utilizando o botão de **permissões de subvenção** é atualmente necessária para aplicações de uma página única (SPA) que utilizam ADAL.js. Caso contrário, o pedido falha quando o token de acesso é solicitado.

## <a name="next-steps"></a>Passos seguintes

* Veja [como converter uma app para ser multi-inquilino](howto-convert-app-to-be-multi-tenant.md)
* Para obter mais profundidade, saiba como o consentimento é suportado na camada de [protocolo OAuth 2.0 durante o fluxo de concessão do código](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) de autorização.
