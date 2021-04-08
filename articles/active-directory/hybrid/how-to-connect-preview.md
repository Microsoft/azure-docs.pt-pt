---
title: 'Azure AD Connect: Funcionalidades em pré-visualização | Microsoft Docs'
description: Este tópico descreve em mais detalhes funcionalidades que estão em pré-visualização no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d48cd8f95ecaf3e537a5221d766af150a51aa31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517689"
---
# <a name="more-details-about-features-in-preview"></a>Mais detalhes sobre funcionalidades na pré-visualização
Este tópico descreve como usar funcionalidades atualmente em pré-visualização.

## <a name="azure-ad-connect-sync-v2-endpoint-api"></a>API de ponto final do Azure AD Connect sync V2

Implementamos um novo ponto final (API) para o Azure AD Connect que melhora o desempenho das operações de serviço de sincronização para o Azure Ative Directory. Utilizando o novo ponto final V2, irá experimentar ganhos de desempenho notáveis na exportação e importação para a Azure AD. Este novo ponto final também apoia grupos sincronizados com até 250 mil membros. A utilização deste ponto final também permite-lhe rescrevê-lo os grupos unificados da Microsoft 365, sem limite máximo de adesão, para o seu Ative Directory no local, quando a gravação do grupo estiver ativada. Para mais informações consulte [Azure AD Connect sync V2 endpoint API](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Writeback do utilizador
> [!IMPORTANT]
> A funcionalidade de pré-visualização do utilizador foi removida na atualização de agosto de 2015 para Azure AD Connect. Se o ativou, deverá desativar esta função.
>
>

## <a name="next-steps"></a>Passos seguintes
Continue a sua [instalação personalizada de Azure AD Connect](how-to-connect-install-custom.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
