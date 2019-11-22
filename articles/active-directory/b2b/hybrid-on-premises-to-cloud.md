---
title: Sincronizar contas de parceiros locais para nuvem como usuários B2B-Azure AD
description: Conceda aos parceiros externos gerenciados localmente acesso a recursos locais e de nuvem usando as mesmas credenciais com a colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcc8c0538bb3362818a4172dd42905fd72b19812
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272603"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Conceder acesso às contas de parceiros gerenciadas localmente aos recursos de nuvem usando a colaboração B2B do Azure AD

Antes de Azure Active Directory (Azure AD), as organizações com sistemas de identidade local têm tradicionalmente gerenciadas contas de parceiros em seu diretório local. Nessa organização, quando você começa a mover aplicativos para o Azure AD, você deseja garantir que seus parceiros possam acessar os recursos de que precisam. Não importa se os recursos estão no local ou na nuvem. Além disso, você deseja que os usuários do parceiro possam usar as mesmas credenciais de entrada para recursos locais e do AD do Azure. 

Se você criar contas para seus parceiros externos em seu diretório local (por exemplo, criar uma conta com um nome de entrada "wmoran" para um usuário externo chamado Wendy Moran no domínio partners.contoso.com), agora você poderá sincronizar essas contas com o nuvem. Especificamente, você pode usar Azure AD Connect para sincronizar as contas de parceiro para a nuvem como usuários B2B do Azure AD (ou seja, usuários com UserType = convidado). Isso permite que os usuários do parceiro acessem recursos de nuvem usando as mesmas credenciais que suas contas locais, sem conceder a eles mais acesso do que precisam. 

## <a name="identify-unique-attributes-for-usertype"></a>Identificar atributos exclusivos para UserType

Antes de habilitar a sincronização do atributo UserType, primeiro você deve decidir como derivar o atributo UserType do Active Directory local. Em outras palavras, quais parâmetros em seu ambiente local são exclusivos para seus colaboradores externos? Determine um parâmetro que distingue esses colaboradores externos dos membros de sua própria organização.

Duas abordagens comuns para isso são:

- Designe um atributo de Active Directory local não utilizado (por exemplo, extensionAttribute1) para usar como o atributo de origem. 
- Como alternativa, derive o valor do atributo UserType de outras propriedades. Por exemplo, você deseja sincronizar todos os usuários como convidados se seu local Active Directory atributo UserPrincipalName termina com o domínio *\@Partners.contoso.com*.
 
Para obter os requisitos de atributo detalhados, consulte [habilitar a sincronização de UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurar Azure AD Connect para sincronizar usuários com a nuvem

Depois de identificar o atributo exclusivo, você pode configurar Azure AD Connect para sincronizar esses usuários com a nuvem como usuários B2B do Azure AD (ou seja, usuários com UserType = convidado). De um ponto de vista de autorização, esses usuários são indistinguíveis de usuários B2B criados por meio do processo de convite de colaboração B2B do Azure AD.

Para obter instruções de implementação, consulte [habilitar a sincronização de UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Passos seguintes

- [Azure Active Directory colaboração B2B para organizações híbridas](hybrid-organizations.md)
- [Os utilizadores de concessão B2B no Azure AD acedem às suas aplicações no local](hybrid-cloud-to-on-premises.md)
- Para obter uma visão geral do Azure AD Connect, consulte [integrar seus diretórios locais com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

