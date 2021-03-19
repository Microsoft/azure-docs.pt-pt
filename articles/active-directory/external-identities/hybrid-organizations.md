---
title: Colaboração B2B para organizações híbridas - Azure AD
description: Dar aos parceiros acesso tanto no local como em recursos em nuvem com colaboração Azure AD B2B.
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
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87909399"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Colaboração B2B do Diretório Ativo Azure para organizações híbridas

A colaboração B2B do Azure Ative Directory (Azure AD) facilita-lhe o acesso aos seus parceiros externos a apps e recursos na sua organização. Isto é verdade mesmo numa configuração híbrida onde você tem tanto no local como recursos baseados em nuvem. Não importa se gere atualmente contas de parceiros externos localmente no seu sistema de identidade no local, ou se gere as contas externas na nuvem como utilizadores do Azure AD B2B. Pode agora conceder a estes utilizadores acesso a recursos em qualquer local, utilizando as mesmas credenciais de inscrição para ambos os ambientes.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Grant B2B utilizadores em Azure AD acesso às suas aplicações no local

Se a sua organização utilizar capacidades de colaboração Azure AD B2B para convidar utilizadores convidados de organizações parceiras para o seu AD Azure, pode agora fornecer a estes utilizadores B2B acesso a aplicações no local.

Para aplicações que utilizem a autenticação baseada em SAML, pode disponibilizar estas aplicações aos utilizadores B2B através do portal Azure, utilizando o Azure AD Application Proxy para autenticação.

Para aplicações que utilizam autenticação integrada do Windows (IWA) com delegação restrita kerberos (KCD), também utiliza o Azure AD Proxy para autenticação. No entanto, para autorização para funcionar, é necessário um objeto de utilizador no Windows Server Ative Directory. Existem dois métodos que pode utilizar para criar objetos de utilizador locais que representam os seus utilizadores convidados B2B.

- Pode utilizar o Microsoft Identity Manager (MIM) 2016 SP1 e o agente de gestão MIM para o Microsoft Graph.
- Pode usar um script PowerShell. (Esta solução não requer MIM.)

Para mais detalhes sobre como implementar estas soluções, consulte [os utilizadores do Grant B2B no Azure AD acesso às suas aplicações no local.](hybrid-cloud-to-on-premises.md)

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Conceder contas de parceiros geridos localmente acesso a recursos na nuvem

Antes da Azure AD, as organizações com sistemas de identidade no local têm tradicionalmente gerido contas de parceiros no seu diretório no local. Se é uma organização assim, quer ter a certeza de que os seus parceiros continuam a ter acesso à medida que move as suas apps e outros recursos para a nuvem. Idealmente, pretende que estes utilizadores utilizem o mesmo conjunto de credenciais para aceder tanto aos recursos em nuvem como no local. 

Agora oferecemos métodos onde você pode usar Azure AD Connect para sincronizar estas contas locais para a nuvem como "utilizadores convidados", onde as contas se comportam como os utilizadores AZure AD B2B.

Para ajudar a proteger os dados da sua empresa, pode controlar o acesso aos recursos certos e configurar políticas de autorização que tratem estes utilizadores convidados de forma diferente dos seus colaboradores.

Para obter detalhes de implementação, consulte [grant contas de parceiro geridas localmente acesso a recursos em nuvem usando a colaboração Azure AD B2B](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Passos seguintes

- [Conceder aos utilizadores B2B no AAD acesso às aplicações no local](hybrid-cloud-to-on-premises.md)
- [Conceder aos recursos na cloud acesso às contas dos parceiros geridas localmente com a colaboração B2B do AAD](hybrid-on-premises-to-cloud.md)


