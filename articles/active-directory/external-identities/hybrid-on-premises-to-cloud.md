---
title: Sync contas de parceiros locais para cloud como utilizadores B2B - Azure AD
description: Dar aos parceiros externos geridos localmente acesso a recursos locais e em nuvem usando as mesmas credenciais com a colaboração Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/03/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29aeca30e1fbdd28d3f69597d902a9b714056cd5
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575928"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Conceder aos recursos na cloud acesso às contas dos parceiros geridas localmente com a colaboração B2B do AAD

Antes do Azure Ative Directory (Azure AD), as organizações com sistemas de identidade no local têm tradicionalmente gerido contas de parceiros no seu diretório no local. Numa organização destas, quando começas a mover apps para Azure AD, queres ter a certeza que os teus parceiros podem aceder aos recursos de que precisam. Não importa se os recursos estão no local ou na nuvem. Além disso, deseja que os utilizadores do seu parceiro possam utilizar as mesmas credenciais de inscrição tanto para os recursos no local como para os recursos AZure AD. 

Se criar contas para os seus parceiros externos no seu diretório no local (por exemplo, criar uma conta com o nome de "wmoran" para um utilizador externo chamado Wendy Moran no seu domínio partners.contoso.com), pode agora sincronizar estas contas na nuvem. Especificamente, pode utilizar o Azure AD Connect para sincronizar as contas do parceiro na nuvem, que cria uma conta de utilizador com o UserType = Guest. Isto permite que os utilizadores do seu parceiro acedam a recursos na nuvem usando as mesmas credenciais que as suas contas locais, sem lhes dar mais acesso do que precisam.

> [!NOTE]
> Veja também como [convidar utilizadores internos para a colaboração B2B](invite-internal-users.md). Com esta funcionalidade, pode convidar os utilizadores internos a utilizarem a colaboração B2B, independentemente de terem sincronizado as suas contas desde o seu diretório no local até à nuvem. Assim que o utilizador aceitar o convite para utilizar a colaboração B2B, poderão usar as suas próprias identidades e credenciais para iniciar seduções nos recursos a que pretende aceder. Não precisará de manter senhas ou gerir ciclos de vida de conta.

## <a name="identify-unique-attributes-for-usertype"></a>Identificar atributos únicos para UserType

Antes de ativar a sincronização do atributo UserType, tem primeiro de decidir como derivar o atributo UserType a partir do Diretório Ativo no local. Por outras palavras, que parâmetros no seu ambiente no local são exclusivos dos seus colaboradores externos? Determine um parâmetro que distinga estes colaboradores externos de membros da sua própria organização.

Duas abordagens comuns para isto são:

- Designar um atributo ative directy não utilizado no local (por exemplo, extensãoAttribute1) para usar como atributo de origem. 
- Em alternativa, obtém-se o valor para atributo UserType de outras propriedades. Por exemplo, pretende sincronizar todos os utilizadores como Guest se o seu atributo Ative Directory UserPrincipalName terminar com o domínio *\@ partners.contoso.com*.
 
Para obter requisitos de atributos detalhados, consulte [Ativar a sincronização do UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurar Azure AD Connect para sincronizar os utilizadores na nuvem

Depois de identificar o atributo único, pode configurar o Azure AD Connect para sincronizar estes utilizadores na nuvem, o que cria uma conta de utilizador com o UserType = Guest. Do ponto de vista da autorização, estes utilizadores são indistinguíveis dos utilizadores B2B criados através do processo de convite de colaboração Azure AD B2B.

Para obter instruções de implementação, consulte [Ativar a sincronização do UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Passos seguintes

- [Colaboração B2B do Diretório Ativo Azure para organizações híbridas](hybrid-organizations.md)
- [Conceder aos utilizadores B2B no AAD acesso às aplicações no local](hybrid-cloud-to-on-premises.md)
- Para uma visão geral do Azure AD Connect, consulte [Integrar os seus diretórios no local com o Azure Ative Directory](../hybrid/whatis-hybrid-identity.md).

