---
title: Framework de consentimento do Azure Active Directory
description: Saiba mais sobre a estrutura de consentimento do Azure Active Directory e como ela torna mais fácil desenvolver aplicações clientes nativas e web de multi-inquilino.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: celested
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0200aa917894f4b7f1a45a9d3bd15b128aaf27a2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124189"
---
# <a name="azure-active-directory-consent-framework"></a>Framework de consentimento do Azure Active Directory

A estrutura de consentimento do Azure Active Directory (Azure AD) torna mais fácil desenvolver aplicações clientes nativas e web de multi-inquilino. Esses aplicativos permitem início de sessão por contas de utilizador de um inquilino do Azure AD que é diferente em que a aplicação fica registada. Também poderá acessar a web, APIs, como o Microsoft Graph API (para acesso do Azure AD, o Intune e serviços do Office 365) e APIs de outros serviços da Microsoft, além de suas próprias APIs web.

O framework baseia-se um utilizador ou um administrador a dar o consentimento para um aplicativo que solicita a registar no seu diretório, o que pode envolver a aceder a dados do diretório. Por exemplo, se precisar de uma aplicação de cliente da web ler as informações de calendário sobre o utilizador do Office 365, esse utilizador é necessário para dar consentimento para a aplicação cliente pela primeira vez. Depois de consentimento for dado, o aplicativo cliente poderá chamar o Microsoft Graph API em nome do utilizador e utilize as informações de calendário, conforme necessário. O [Microsoft Graph API](https://developer.microsoft.com/graph) fornece acesso a dados no Office 365 (como calendários e mensagens do Exchange, sites e listas do SharePoint, documentos do OneDrive, blocos de notas do OneNote, tarefas do Planner e pastas de trabalho de Excel), bem como os utilizadores e grupos do Azure AD e outros objetos de dados de mais serviços cloud da Microsoft.

Estrutura do consentimento baseia-se em OAuth 2.0 e os vários fluxos, como concedem credenciais de cliente e de concessão do código de autorização, usando clientes públicos ou confidenciais. Ao utilizar o OAuth 2.0, do Azure AD torna possível criar vários tipos de aplicações de cliente, tal como num telemóvel, tablet, servidor ou uma aplicação web – e obter acesso aos recursos necessários.

Para mais informações sobre como utilizar a estrutura do consentimento com concessões de autorização de OAuth2.0, veja [autorizar o acesso a aplicações web com OAuth 2.0 e o Azure AD](v1-protocols-oauth-code.md) e [cenários de autenticação do Azure AD](authentication-scenarios.md). Para informações sobre como obter acesso autorizado ao Office 365 através do Microsoft Graph, veja [autenticação de aplicações com o Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Experiência de consentimento - um exemplo

Os passos seguintes mostram como o consentimento refletir funciona para o desenvolvedor do aplicativo e o utilizador.

1. Suponha que tem uma aplicação de cliente web que precisa para solicitar permissões específicas para aceder a uma recurso/API. Ficará a saber como fazer esta configuração na secção seguinte, mas, essencialmente, o portal do Azure é utilizado para declarar as solicitações de permissão no momento da configuração. Como outras definições de configuração, que se tornem parte do registo da aplicação do Azure AD:

    ![Permissões para outras aplicações](./media/quickstart-v1-integrate-apps-with-azure-ad/requiredpermissions.png)

1. Considere que foram atualizadas as permissões de seu aplicativo, a execução do aplicativo e um utilizador estiver prestes a utilizá-lo pela primeira vez. Em primeiro lugar, a aplicação tem de obter um código de autorização do Azure AD `/authorize` ponto final. O código de autorização, em seguida, pode ser usado para adquirir um novo acesso e atualizar o token.

1. Se o utilizador já não for autenticado, o Azure do AD `/authorize` endpoint pede ao utilizador para iniciar sessão.

    [Utilizador ou administrador de início de sessão para o Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. Depois do utilizador tem sessão iniciada, do Azure AD irá determinar se o utilizador tem de ser apresentada uma página de consentimento. Essa determinação baseia-se o utilizador (ou o administrador da sua organização) já concedeu o consentimento da aplicação. Se já não tiver sido concedido consentimento, o Azure AD solicita o consentimento do usuário e exibe as permissões necessárias para funcionar. O conjunto de permissões que são apresentadas na caixa de diálogo de consentimento corresponder aos perfis selecionados na **permissões delegadas** no portal do Azure.

    ![Experiência de consentimento do utilizador](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. Depois do utilizador concede consentimento, é devolvido um código de autorização para seu aplicativo, que é resgatado para adquirir um token de acesso e atualizar o token. Para obter mais informações sobre este fluxo, consulte [tipo de aplicação de Web API](web-api.md).

1. Como administrador, pode também autorizar permissões delegadas de uma aplicação em nome de todos os utilizadores no seu inquilino. Consentimento administrativo impede que a caixa de diálogo de consentimento que aparece para cada utilizador no inquilino e pode ser feito no [portal do Azure](https://portal.azure.com) por usuários com a função de administrador. Para saber que funções podem dar consentimento a permissões delegadas de administrador, veja [permissões da função de administrador no Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Para dar consentimento a uma aplicação do permissões delegadas**

   1. Vá para o **definições** página para a sua aplicação
   1. Selecione **permissões obrigatórias**.
   1. Clique nas **conceder permissões** botão.

      ![Concessão de permissões de consentimento de administrador explícita](./media/quickstart-v1-integrate-apps-with-azure-ad/grantpermissions.png)

   > [!IMPORTANT]
   > Conceder explícita consentir utilizar o **conceder permissões** botão é atualmente necessário para aplicações de página única (SPA) que utilizam ADAL.js. Caso contrário, o aplicativo falhar quando o token de acesso é solicitado.

## <a name="next-steps"></a>Passos Seguintes

* Consulte [como converter um aplicativo de modo a multi-inquilino](howto-convert-app-to-be-multi-tenant.md)
* Para obter mais detalhes, saiba [como o consentimento é suportado na camada de protocolo de OAuth 2.0 durante o fluxo de concessão do código de autorização.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
