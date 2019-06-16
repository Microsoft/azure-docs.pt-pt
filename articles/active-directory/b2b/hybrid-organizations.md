---
title: Colaboração B2B para organizações híbridas - Azure Active Directory | Documentos da Microsoft
description: Dar aos parceiros acesso a ambos os locais e recursos com a colaboração B2B do Azure AD na cloud.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: b30e9c14863f67d28203eac916606e8786101e3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65768287"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Colaboração do Azure Active Directory B2B para organizações híbridas

Colaboração do Azure Active Directory (Azure AD) B2B torna mais fácil para conceder aos seus parceiros externos acesso a aplicações e recursos na sua organização. Isso é verdadeiro, mesmo numa configuração híbrida em que tiver no local e recursos baseados na nuvem. Não importa se gerencia atualmente contas de parceiros externos localmente no seu sistema de identidade no local, ou se gerir as contas externas na cloud, como utilizadores do Azure AD B2B. Pode agora conceder a estes utilizadores acesso a recursos em ambos os locais, com as mesmas credenciais de início de sessão para ambos os ambientes.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Os utilizadores de concessão B2B no Azure AD acedem às suas aplicações no local

Se sua organização utiliza capacidades de colaboração B2B do Azure AD para convidar utilizadores de organizações parceiras com o Azure AD, agora pode fornecer aos utilizadores B2B acesso a aplicações no local.

Para aplicações que utilizam a autenticação baseada no SAML, é possível disponibilizar estas aplicações para utilizadores do B2B através do portal do Azure, com o Proxy de aplicações do Azure AD para autenticação.

Para aplicações que utilizam a autenticação integrada do Windows (IWA) com a delegação restringida de Kerberos (KCD), também é usar Proxy do Azure AD para autenticação. No entanto, para autorização funcione, é obrigatório um objeto de utilizador no local Windows Server Active Directory. Existem dois métodos que pode utilizar para criar objetos de utilizador local que representam os utilizadores de convidados B2B.

- Pode usar o Microsoft Identity Manager (MIM) 2016 SP1 e o agente de gestão de MIM para o Microsoft Graph.
- Pode usar um script do PowerShell. (Esta solução não necessita de MIM.)

Para obter detalhes sobre como implementar estas soluções, veja [B2B de concessão de utilizadores no Azure AD acedem às suas aplicações no local](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Conceder acesso de contas do parceiro gerido localmente a recursos na cloud

Antes do Azure AD, as organizações com sistemas de identidade no local têm tradicionalmente parceiro contas geridas no seu diretório no local. Se for uma organização desse tipo, que pretende certificar-se de que os seus parceiros continuam a ter acesso, como mover as suas aplicações e outros recursos para a cloud. Idealmente, pretende que estes utilizadores a utilizar o mesmo conjunto de credenciais para aceder aos recursos na cloud e no local. 

Vamos agora os métodos de oferta em que pode utilizar o Azure AD Connect para sincronizar essas contas locais para a cloud como "os utilizadores convidados", onde as contas comportar-se apenas como utilizadores do Azure AD B2B.

Para ajudar a proteger os seus dados da empresa, pode controlar o acesso aos recursos certos e configurar políticas de autorização que tratam estes utilizadores convidados forma diferente dos seus funcionários.

Para obter detalhes de implementação, consulte [contas de parceiros localmente geridos de concessão de acesso a recursos na cloud através da colaboração B2B do Azure AD](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Passos Seguintes

- [Os utilizadores de concessão B2B no Azure AD acedem às suas aplicações no local](hybrid-cloud-to-on-premises.md)
- [Conceder acesso de contas de parceiros gerido localmente a recursos na cloud através da colaboração B2B do Azure AD](hybrid-on-premises-to-cloud.md)


