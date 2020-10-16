---
title: Visualização de aplicativos usando o seu inquilino Azure Ative Directory para gestão de identidade
description: Compreenda como visualizar todas as aplicações utilizando o seu inquilino Azure Ative Directory para gestão de identidade.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 5229b123986149903d562bc0b3d6902c0136f647
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707888"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Visualização de aplicativos usando o seu inquilino Azure AD para gestão de identidade
A [Série Quickstart em Gestão de Aplicações](view-applications-portal.md) leva-lhe o básico. Nele, você aprende a ver todas as aplicações usando o seu inquilino AZure AD para gestão de identidade. Este artigo mergulha um pouco mais nos tipos de apps que vai encontrar.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Por que uma aplicação específica aparece na minha lista de todos os pedidos?
Quando filtrado para **todas as aplicações,** a **Lista** de Todas as Aplicações mostra todos os **objetos** principais de serviço no seu inquilino. Os objetos principais de serviço podem aparecer nesta lista de várias formas:
- Quando adicionar qualquer aplicação na galeria de aplicações, incluindo:
   - **Azure AD - Aplicações empresariais** – Aplicações adicionadas ao seu inquilino utilizando a opção **de aplicações Da Enterprise** no portal AD Azure. Normalmente, aplicações integradas usando a norma SAML.
   - **Azure AD - Registos de aplicações** – Aplicações adicionadas ao seu inquilino utilizando a opção **de registos da App** no portal AD AZure. Normalmente, aplicações desenvolvidas personalizadas utilizando os padrões Open ID Connect e OAuth.
   - **Aplicações Proxy Applications** – Uma aplicação em execução no seu ambiente no local que pretende fornecer um único sinal seguro para externamente
- Ao inscrever-se ou iniciar sessão numa aplicação de terceiros integrada com o Azure Ative Directory. Um exemplo é [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- Aplicações da Microsoft como a Microsoft 365.
- Quando adiciona um novo registo de candidaturas criando uma aplicação desenvolvida por medida utilizando o [Registo de Pedidos](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)
- Quando adiciona um novo registo de candidaturas criando uma aplicação personalizada utilizando o [portal V2.0 Application Registration](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)
- Ao adicionar uma aplicação, está a desenvolver-se utilizando [os métodos de autenticação ASP.NET](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) do Visual Studio ou [serviços conectados](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- Quando cria um objeto principal de serviço utilizando o [Módulo Azure AD PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)
- Quando [consente num pedido](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) como administrador para utilizar dados no seu inquilino
- Quando um [utilizador consente com uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) para utilizar dados no seu inquilino
- Quando ativa determinados serviços que armazenam dados no seu inquilino. Um exemplo é o Reset da Palavra-Passe, que é modelado como um principal de serviço para armazenar a sua política de reset de palavra-passe de forma segura.

Saiba mais sobre como e porquê, as aplicações são adicionadas ao seu diretório, veja [como as aplicações são adicionadas ao AZure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)

## <a name="next-steps"></a>Passos seguintes
[Gestão de Aplicações com Diretório Ativo Azure](what-is-application-management.md)
