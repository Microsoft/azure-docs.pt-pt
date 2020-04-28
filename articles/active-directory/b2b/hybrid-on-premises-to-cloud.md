---
title: Sync contas de parceirolocal para cloud como utilizadores B2B - Azure AD
description: Dê aos parceiros externos geridos localmente acesso aos recursos locais e na nuvem utilizando as mesmas credenciais com colaboração Azure AD B2B.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272603"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Conceder contas de parceiro geridas localmente acesso a recursos na nuvem usando a colaboração Azure AD B2B

Antes do Azure Ative Directory (Azure AD), as organizações com sistemas de identidade no local têm gerido tradicionalmente contas de parceiros no seu diretório no local. Numa organização deste tipo, quando começa a mover aplicações para a AD Azure, pretende certificar-se de que os seus parceiros podem aceder aos recursos de que necessitam. Não importa se os recursos estão no local ou na nuvem. Além disso, pretende que os utilizadores parceiros possam utilizar as mesmas credenciais de entrada tanto no local como para os recursos da Azure AD. 

Se criar contas para os seus parceiros externos no seu diretório no local (por exemplo, cria uma conta com o nome de "wmoran" para um utilizador externo chamado Wendy Moran no seu domínio partners.contoso.com), pode agora sincronizar essas contas na nuvem. Especificamente, pode utilizar o Azure AD Connect para sincronizar as contas do parceiro na nuvem como utilizadores Do Azure AD B2B (isto é, utilizadores com UserType = Guest). Isto permite aos utilizadores parceiros aceder em recursos na nuvem usando as mesmas credenciais que as suas contas locais, sem lhes dar mais acesso do que precisam. 

## <a name="identify-unique-attributes-for-usertype"></a>Identificar atributos únicos para userType

Antes de ativar a sincronização do atributo UserType, tem primeiro de decidir como obter o atributo userType a partir do Diretório Ativo no local. Por outras palavras, que parâmetros no seu ambiente no local são exclusivos dos seus colaboradores externos? Determine um parâmetro que distinga estes colaboradores externos de membros da sua própria organização.

Duas abordagens comuns para isto são:

- Designe um atributo de Diretório Ativo não utilizado no local (por exemplo, extensãoAttribute1) para usar como atributo de origem. 
- Alternativamente, obtenha o valor para atributo userType de outras propriedades. Por exemplo, pretende sincronizar todos os utilizadores como Guest se o seu atributo ative directoryPrincipalName terminar com o domínio * \@partners.contoso.com*.
 
Para obter requisitos de atributos detalhados, consulte [Ativação de sincronização do UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configure Azure AD Connect para sincronizar os utilizadores da nuvem

Depois de identificar o atributo único, pode configurar o Azure AD Connect para sincronizar estes utilizadores na nuvem como utilizadores Do Azure AD B2B (isto é, utilizadores com UserType = Guest). Do ponto de vista da autorização, estes utilizadores são indistinguíveis dos utilizadores B2B criados através do processo de convite de colaboração Azure AD B2B.

Para obter instruções de implementação, consulte Ativar a [sincronização do UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Passos seguintes

- [Colaboração Azure Ative Directory B2B para organizações híbridas](hybrid-organizations.md)
- [Conceder aos utilizadores B2B no Acesso a AD Azure às suas aplicações no local](hybrid-cloud-to-on-premises.md)
- Para uma visão geral do Azure AD Connect, consulte [Integrar os seus diretórios no local com o Diretório Ativo Azure](../hybrid/whatis-hybrid-identity.md).

