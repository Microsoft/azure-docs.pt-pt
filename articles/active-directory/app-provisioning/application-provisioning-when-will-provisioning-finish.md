---
title: O fornecimento de utilizadores à app Azure AD Gallery está a demorar horas ou mais
description: Como descobrir por que o fornecimento à sua aplicação pode estar a demorar mais tempo do que esperava
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522650"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>O fornecimento de utilizadores a uma aplicação da Galeria AD Azure está a demorar horas ou mais

Ao permitir o fornecimento automático para uma aplicação, o ciclo inicial pode demorar entre 20 minutos e várias horas, dependendo da dimensão do diretório DaD Azure e do número de utilizadores no âmbito do provisionamento. 

As sincronizações subsequentes após o ciclo inicial são mais rápidas, uma vez que o serviço de fornecimento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes.

## <a name="how-to-improve-provisioning-performance"></a>Como melhorar o desempenho do provisionamento

Se o ciclo inicial demorar mais do que algumas horas, há uma coisa que pode fazer para melhorar o desempenho:

-   **Filtros de deteção do utilizador.** Os filtros de deteção permitem afinar os dados que o serviço de fornecimento extrai da AD Azure filtrando os utilizadores com base em valores específicos de atributos. Para obter mais informações sobre filtros de deteção, consulte o [fornecimento de aplicações baseado no Atributo com filtros](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)de deteção .

## <a name="next-steps"></a>Passos seguintes
[Automatizar o fornecimento e o deprovisionamento de utilizadores às aplicações SaaS com diretório ativo Azure](user-provisioning.md)

