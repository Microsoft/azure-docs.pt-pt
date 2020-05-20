---
title: 'Azure AD Connect: Funcionalidades na pré-visualização Microsoft Docs'
description: Este tópico descreve em mais detalhes as funcionalidades que estão em pré-visualização no Azure AD Connect.
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
ms.topic: conceptual
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396e1d9e6ad474d053ca803218d55396c073845d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680193"
---
# <a name="more-details-about-features-in-preview"></a>Mais detalhes sobre funcionalidades na pré-visualização
Este tópico descreve como usar funcionalidades atualmente em pré-visualização.

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect sync V2 endpoint API (pré-visualização pública) 

Implementámos um novo ponto final (API) para o Azure AD Connect que melhora o desempenho das operações de serviço de sincronização para o Azure Ative Directory. Ao utilizar o novo ponto final do V2, irá experimentar ganhos de desempenho visíveis na exportação e importação para a Azure AD. Este novo ponto final também apoia grupos sincronizados com até 250 mil membros. A utilização deste ponto final também permite-lhe reescrever os grupos unificados O365, sem limite máximo de adesão, ao seu Diretório Ativo no local, quando a reescrita em grupo estiver ativada.   Para mais informações consulte [Azure AD Connect sync V2 endpoint API (pré-visualização pública)](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Redação do utilizador
> [!IMPORTANT]
> A funcionalidade de pré-visualização de reprodução do utilizador foi removida na atualização de agosto de 2015 para o Azure AD Connect. Se o ativou, deverá desativar esta funcionalidade.
>
>

## <a name="next-steps"></a>Passos seguintes
Continue a [instalação personalizada de Azure AD Connect](how-to-connect-install-custom.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
