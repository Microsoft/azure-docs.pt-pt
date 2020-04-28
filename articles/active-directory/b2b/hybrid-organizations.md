---
title: Colaboração B2B para organizações híbridas - Azure AD
description: Dê aos parceiros acesso tanto aos recursos no local como aos recursos na nuvem com a colaboração Azure AD B2B.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272478"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Colaboração Azure Ative Directory B2B para organizações híbridas

A colaboração Do Azure Ative Directory (Azure AD) B2B facilita-lhe o acesso aos seus parceiros externos a apps e recursos na sua organização. Isto é verdade mesmo numa configuração híbrida onde você tem tanto no local como recursos baseados em nuvem. Não importa se gere atualmente contas de parceiros externos localmente no seu sistema de identidade no local, ou se gere as contas externas na nuvem como utilizadores Do Azure AD B2B. Pode agora conceder a estes utilizadores acesso aos recursos em ambos os locais, utilizando as mesmas credenciais de inscrição para ambos os ambientes.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Conceder aos utilizadores B2B no Acesso a AD Azure às suas aplicações no local

Se a sua organização utilizar capacidades de colaboração Azure AD B2B para convidar utilizadores convidados de organizações parceiras para o seu Azure AD, pode agora fornecer a estes utilizadores B2B acesso a aplicações no local.

Para aplicações que utilizem a autenticação baseada em SAML, pode disponibilizar estas aplicações aos utilizadores B2B através do portal Azure, utilizando o Proxy de Aplicação AD Azure para autenticação.

Para aplicações que utilizem a Autenticação Integrada do Windows (IWA) com a delegação limitada kerberos (KCD), também utiliza o Proxy Azure AD para autenticação. No entanto, para autorização para funcionar, é necessário um objeto de utilizador no Diretório Ativo do Servidor do Windows no local. Existem dois métodos que pode utilizar para criar objetos de utilizador locais que representam os seus utilizadores convidados B2B.

- Pode utilizar o Microsoft Identity Manager (MIM) 2016 SP1 e o agente de gestão MIM para o Microsoft Graph.
- Pode usar um script PowerShell. (Esta solução não requer MIM.)

Para mais detalhes sobre como implementar estas soluções, consulte os utilizadores do [Grant B2B no Azure AD acesso às suas aplicações no local.](hybrid-cloud-to-on-premises.md)

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Conceder contas de parceiro geridas localmente acesso a recursos na nuvem

Antes da Azure AD, as organizações com sistemas de identidade no local têm tradicionalmente gerido contas de parceiros no seu diretório no local. Se é uma organização assim, pretende certificar-se de que os seus parceiros continuam a ter acesso à medida que transfere as suas apps e outros recursos para a nuvem. Idealmente, pretende que estes utilizadores utilizem o mesmo conjunto de credenciais para aceder tanto aos recursos em nuvem como no local. 

Oferecemos agora métodos onde pode utilizar o Azure AD Connect para sincronizar estas contas locais na nuvem como "utilizadores convidados", onde as contas se comportam como utilizadores do Azure AD B2B.

Para ajudar a proteger os dados da sua empresa, pode controlar o acesso aos recursos certos e configurar políticas de autorização que tratem estes utilizadores convidados de forma diferente dos seus colaboradores.

Para mais detalhes de implementação, consulte grant [localmente gerido parceiro contas acesso a recursos cloud usando a colaboração Azure AD B2B](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Passos seguintes

- [Conceder aos utilizadores B2B no Acesso a AD Azure às suas aplicações no local](hybrid-cloud-to-on-premises.md)
- [Conceder contas de parceiro geridas localmente acesso a recursos na nuvem usando a colaboração Azure AD B2B](hybrid-on-premises-to-cloud.md)


