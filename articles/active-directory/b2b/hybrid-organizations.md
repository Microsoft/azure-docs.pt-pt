---
title: Colaboração B2B para organizações híbridas-Azure AD
description: Dê aos parceiros acesso a recursos locais e na nuvem com a colaboração B2B do Azure AD.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272478"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory colaboração B2B para organizações híbridas

A colaboração B2B do Azure Active Directory (Azure AD) facilita a permissão de acesso de seus parceiros externos a aplicativos e recursos em sua organização. Isso é verdadeiro mesmo em uma configuração híbrida em que você tem recursos locais e baseados em nuvem. Não importa se você atualmente gerencia contas de parceiros externas localmente no seu sistema de identidade local ou se gerencia as contas externas na nuvem como usuários B2B do Azure AD. Agora você pode conceder a esses usuários acesso aos recursos em qualquer local, usando as mesmas credenciais de entrada para ambos os ambientes.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Conceder aos usuários B2B no Azure AD acesso aos seus aplicativos locais

Se sua organização usa recursos de colaboração B2B do Azure AD para convidar usuários convidados de organizações parceiras para o Azure AD, agora você pode fornecer a esses usuários B2B acesso a aplicativos locais.

Para aplicativos que usam autenticação baseada em SAML, você pode disponibilizar esses aplicativos para usuários B2B por meio do portal do Azure, usando o Proxy de Aplicativo do AD do Azure para autenticação.

Para aplicativos que usam a IWA (autenticação integrada do Windows) com KCD (delegação restrita de Kerberos), você também usa o proxy do Azure AD para autenticação. No entanto, para que a autorização funcione, um objeto de usuário é necessário no Windows Server Active Directory local. Há dois métodos que você pode usar para criar objetos de usuário local que representam seus usuários convidados B2B.

- Você pode usar o Microsoft Identity Manager (MIM) 2016 SP1 e o agente de gerenciamento do MIM para Microsoft Graph.
- Você pode usar um script do PowerShell. (Essa solução não exige o MIM.)

Para obter detalhes sobre como implementar essas soluções, consulte [conceder aos usuários B2B no Azure ad acesso aos seus aplicativos locais](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Conceder acesso às contas de parceiros gerenciadas localmente aos recursos de nuvem

Antes do Azure AD, as organizações com sistemas de identidade local têm tradicionalmente gerenciadas contas de parceiros em seu diretório local. Se você for uma organização desse tipo, você deseja garantir que seus parceiros continuem a ter acesso à medida que você move seus aplicativos e outros recursos para a nuvem. Idealmente, você quer que esses usuários usem o mesmo conjunto de credenciais para acessar recursos de nuvem e locais. 

Agora, oferecemos métodos em que você pode usar Azure AD Connect para sincronizar essas contas locais com a nuvem como "usuários convidados", onde as contas se comportam exatamente como usuários B2B do Azure AD.

Para ajudar a proteger os dados da empresa, você pode controlar o acesso apenas aos recursos corretos e configurar as políticas de autorização que tratam esses usuários convidados de forma diferente dos seus funcionários.

Para obter detalhes de implementação, consulte [conceder acesso às contas de parceiros gerenciadas localmente para recursos de nuvem usando a colaboração B2B do Azure ad](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Passos seguintes

- [Os utilizadores de concessão B2B no Azure AD acedem às suas aplicações no local](hybrid-cloud-to-on-premises.md)
- [Conceder acesso às contas de parceiros gerenciadas localmente aos recursos de nuvem usando a colaboração B2B do Azure AD](hybrid-on-premises-to-cloud.md)


