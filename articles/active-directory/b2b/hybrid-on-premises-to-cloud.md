---
title: Sincronizar contas do parceiro local para a cloud como utilizadores B2B - Azure Active Directory | Documentos da Microsoft
description: Dar localmente geridos parceiros externos, acesso a ambos os locais e recursos da nuvem com as mesmas credenciais com a colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c5ec6a41b630ee20139575080d8874d819bb59
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57871363"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Conceder acesso de contas de parceiros gerido localmente a recursos na cloud através da colaboração B2B do Azure AD

Antes do Azure Active Directory (Azure AD), as organizações com sistemas de identidade no local têm contas de parceiros tradicionalmente geridos em seu diretório no local. Numa organização, quando começar a mover aplicações para o Azure AD, que pretende tornar-se que aos seus parceiros podem aceder os recursos de que precisam. Não importa se os recursos estão no local ou na cloud. Além disso, pretende que os utilizadores de parceiro, de conseguir utilizar as mesmas credenciais de início de sessão para recursos do Azure AD e no local. 

Se criar contas para os parceiros externos no seu diretório no local (por exemplo, criar uma conta com um nome de início de sessão de "wmoran" para um utilizador externo com o nome Wendy Moran no seu domínio partners.contoso.com), pode agora sincronizar essas contas para o na cloud. Especificamente, pode utilizar o Azure AD Connect para sincronizar as contas de parceiros para a cloud, como utilizadores B2B do Azure AD (ou seja, os utilizadores com UserType = convidado). Isto permite que os utilizadores parceiros acedam aos recursos da nuvem com as mesmas credenciais como suas contas locais, sem fornecer acesso mais do que o necessário. 

## <a name="identify-unique-attributes-for-usertype"></a>Identificar os atributos exclusivos para UserType

Antes de ativar a sincronização do atributo UserType, primeiro deve decidir como derivar o atributo UserType do Active Directory no local. Em outras palavras, quais os parâmetros no seu ambiente no local são exclusivos para seus funcionários externos? Determine um parâmetro que distingue estes funcionários externos de membros da sua organização.

Duas abordagens comuns para isso são:

- Designe um atributo de Active Directory não utilizado no local (por exemplo, extensionAttribute1) para utilizar como o atributo de origem. 
- Em alternativa, derive o valor para o atributo UserType de outras propriedades. Por exemplo, pretende sincronizar todos os utilizadores como convidado se o atributo de UserPrincipalName do Active Directory no local, terminar com o domínio  *\@partners.contoso.com*.
 
Para requisitos de atributo detalhadas, consulte [ativar a sincronização de UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurar o Azure AD Connect para sincronizar os utilizadores para a cloud

Depois de identificar o atributo exclusivo, pode configurar o Azure AD Connect para sincronizar esses usuários para a cloud, como utilizadores B2B do Azure AD (ou seja, os utilizadores com UserType = convidado). A partir de um autorização ponto de vista, estes utilizadores são não podem ser diferenciados dos utilizadores B2B criados através do processo de convite de colaboração B2B do Azure AD.

Para obter instruções de implementação, consulte [ativar a sincronização de UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Passos Seguintes

- [Colaboração do Azure Active Directory B2B para organizações híbridas](hybrid-organizations.md)
- [Os utilizadores de concessão B2B no Azure AD acedem às suas aplicações no local](hybrid-cloud-to-on-premises.md)
- Para uma descrição geral do Azure AD Connect, consulte [integrar seus diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

