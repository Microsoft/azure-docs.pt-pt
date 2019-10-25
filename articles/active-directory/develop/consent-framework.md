---
title: Estrutura de consentimento Azure Active Directory
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a estrutura de consentimento em Azure Active Directory e como ela facilita o desenvolvimento de aplicativos de cliente nativos e Web multilocatários.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5b60901e57392aaea504f96572801a878d707c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803855"
---
# <a name="azure-active-directory-consent-framework"></a>Estrutura de consentimento Azure Active Directory

A estrutura de consentimento do Azure Active Directory (AD do Azure) facilita o desenvolvimento de aplicativos cliente nativos e Web multilocatários. Esses aplicativos permitem a entrada por contas de usuário de um locatário do Azure AD diferente daquele em que o aplicativo está registrado. Eles também podem precisar acessar APIs da Web, como a API de Microsoft Graph (para acessar o Azure AD, o Intune e os serviços no Office 365) e outras APIs de serviços da Microsoft, além de suas próprias APIs Web.

A estrutura é baseada em um usuário ou administrador que dá consentimento a um aplicativo que solicita ser registrado em seu diretório, o que pode envolver o acesso a dados do diretório. Por exemplo, se um aplicativo cliente Web precisar ler informações de calendário sobre o usuário do Office 365, esse usuário precisará dar consentimento ao aplicativo cliente primeiro. Após o consentimento ser fornecido, o aplicativo cliente poderá chamar a API de Microsoft Graph em nome do usuário e usar as informações de calendário conforme necessário. A [API Microsoft Graph](https://developer.microsoft.com/graph) fornece acesso a dados no Office 365 (como calendários e mensagens do Exchange, sites e listas do SharePoint, documentos do onedrive, blocos de anotações do OneNote, tarefas do Planner e pastas de trabalho do Excel), bem como usuários e grupos do Azure AD e outros objetos de dados de mais serviços em nuvem da Microsoft.

A estrutura de consentimento é criada no OAuth 2,0 e em seus vários fluxos, como concessão de código de autorização e concessão de credenciais de cliente, usando clientes públicos ou confidenciais. Usando o OAuth 2,0, o AD do Azure possibilita criar muitos tipos diferentes de aplicativos cliente, como em um telefone, Tablet, servidor ou aplicativo Web, e obter acesso aos recursos necessários.

Para obter mais informações sobre como usar a estrutura de consentimento com as concessões de autorização do OAuth 2.0, consulte [autorizar o acesso a aplicativos Web usando oauth 2,0 e o Azure ad](v1-protocols-oauth-code.md) e [cenários de autenticação para o Azure ad](authentication-scenarios.md). Para obter informações sobre como obter acesso autorizado ao Office 365 por meio de Microsoft Graph, consulte [autenticação de aplicativo com Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Experiência de consentimento-um exemplo

As etapas a seguir mostram como a experiência de consentimento funciona tanto para o desenvolvedor do aplicativo quanto para o usuário.

1. Suponha que você tenha um aplicativo cliente Web que precisa solicitar permissões específicas para acessar um recurso/API. Você aprenderá como fazer essa configuração na próxima seção, mas, essencialmente, a portal do Azure é usada para declarar solicitações de permissão no momento da configuração. Assim como outras definições de configuração, elas se tornam parte do registro do Azure AD do aplicativo:

    ![Permissões para outros aplicativos](./media/consent-framework/permissions.png)

1. Considere que as permissões do seu aplicativo foram atualizadas, o aplicativo está em execução e um usuário está prestes a usá-lo pela primeira vez. Primeiro, o aplicativo precisa obter um código de autorização do ponto de extremidade `/authorize` do Azure AD. O código de autorização pode então ser usado para adquirir um novo token de acesso e de atualização.

1. Se o usuário ainda não estiver autenticado, o ponto de extremidade de `/authorize` do Azure AD solicitará que o usuário entre.

    ![Usuário ou administrador entrar no Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. Depois que o usuário tiver entrado, o AD do Azure determinará se o usuário precisa ser mostrado na página de consentimento. Essa determinação se baseia em se o usuário (ou o administrador da organização) já concedeu o consentimento do aplicativo. Se o consentimento ainda não tiver sido concedido, o Azure AD solicitará o consentimento do usuário e exibirá as permissões necessárias que ele precisa para funcionar. O conjunto de permissões que são exibidas na caixa de diálogo de consentimento corresponde àqueles selecionados nas **permissões delegadas** no portal do Azure.

    ![Mostra um exemplo de permissões exibidas na caixa de diálogo de consentimento](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. Depois que o usuário concede consentimento, um código de autorização é retornado para seu aplicativo, que é resgatado para adquirir um token de acesso e um token de atualização. Para obter mais informações sobre esse fluxo, consulte [tipo de aplicativo de API Web](web-api.md).

1. Como administrador, você também pode concordar com as permissões delegadas de um aplicativo em nome de todos os usuários em seu locatário. O consentimento administrativo impede que a caixa de diálogo de consentimento apareça para cada usuário no locatário e pode ser feita no [portal do Azure](https://portal.azure.com) por usuários com a função de administrador. Para saber quais funções de administrador podem consentir as permissões delegadas, consulte [permissões de função de administrador no Azure ad](../users-groups-roles/directory-assign-admin-roles.md).

    **Para dar consentimento às permissões delegadas de um aplicativo**

   1. Vá para a página de **permissões de API** para seu aplicativo
   1. Clique no botão **conceder consentimento do administrador** .

      ![Conceder permissões para consentimento de administrador explícito](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Conceder consentimento explícito usando o botão **conceder permissões** é atualmente necessário para Spa (aplicativos de página única) que usam o Adal. js. Caso contrário, o aplicativo falhará quando o token de acesso for solicitado.

## <a name="next-steps"></a>Passos seguintes

* Consulte [como converter um aplicativo para ser multilocatário](howto-convert-app-to-be-multi-tenant.md)
* Para obter mais detalhes, saiba [como o consentimento tem suporte na camada de protocolo OAuth 2,0 durante o fluxo de concessão de código de autorização.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
