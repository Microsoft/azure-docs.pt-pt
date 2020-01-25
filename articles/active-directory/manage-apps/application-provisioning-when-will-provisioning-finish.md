---
title: O fornecimento de utilizadores à app Azure AD Gallery está a demorar horas ou mais
description: Como descobrir por que o fornecimento à sua aplicação pode estar a demorar mais tempo do que esperava
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0c2586b129935043ae7b2eccd11cc3d65a385c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712088"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>O fornecimento de utilizadores a uma aplicação da Galeria AD Azure está a demorar horas ou mais

Ao permitir o fornecimento automático para uma aplicação, o ciclo inicial pode demorar entre 20 minutos e várias horas, dependendo da dimensão do diretório DaD Azure e do número de utilizadores no âmbito do provisionamento. 

As sincronizações subsequentes após o ciclo inicial são mais rápidas, uma vez que o serviço de fornecimento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes.

## <a name="how-to-improve-provisioning-performance"></a>Como melhorar o desempenho do provisionamento

Se o ciclo inicial demorar mais do que algumas horas, há uma coisa que pode fazer para melhorar o desempenho:

-   **Filtros de deteção do utilizador.** Os filtros de escopo permitem ajustar os dados que o serviço de provisionamento extrai do Azure AD filtrando os usuários com base em valores de atributo específicos. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativo baseado em atributo com filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Passos seguintes
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e o desaprovisionamento de utilizadores em Aplicações SaaS com o Azure Active Directory)

