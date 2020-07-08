---
title: O fornecimento de utilizadores à app AZure AD Gallery está a demorar horas ou mais
description: Como descobrir por que o provisionamento à sua aplicação pode estar a demorar mais tempo do que o esperado
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: f0f572e5153b6e79bf7731d0d57b66e601a0054c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781978"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>O fornecimento de utilizadores a uma aplicação da Galeria AD Azure está a demorar horas ou mais

Ao permitir primeiro o provisionamento automático para uma aplicação, o ciclo inicial pode demorar entre 20 minutos e várias horas, dependendo do tamanho do diretório AZure AD e do número de utilizadores em possibilidade de provisionamento. 

As sincronizações subsequentes após o ciclo inicial são mais rápidas, uma vez que o serviço de fornecimento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes.

## <a name="how-to-improve-provisioning-performance"></a>Como melhorar o desempenho do provisionamento

Se o ciclo inicial estiver a demorar mais do que algumas horas, há uma coisa que pode fazer para melhorar o desempenho:

-   **Filtros de deteção do utilizador.** Os filtros de deteção permitem-lhe afinar os dados que o serviço de fornecimento extrai do Azure AD filtrando os utilizadores com base em valores específicos de atributos. Para obter mais informações sobre filtros de deteção, consulte [o provisionamento de aplicações baseados em Atributos com filtros de escoamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Próximos passos
[Automatizar o Provisionamento de Utilizadores e Deprovisionamento para aplicações saas com diretório ativo Azure](user-provisioning.md)

