---
title: Gestão de inquilinos cruzados no Centro de Segurança Azure Microsoft Docs
description: Saiba como criar a gestão de inquilinos cruzados para gerir a postura de segurança de vários inquilinos no Centro de Segurança usando a gestão de recursos delegados do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919486"
---
# <a name="cross-tenant-management-in-security-center"></a>Gestão de inquilinos cruzados no Centro de Segurança

A gestão de inquilinos cruzados permite-lhe visualizar e gerir a postura de segurança de vários inquilinos no Centro de Segurança, alavancando a [gestão de recursos delegados do Azure.](../lighthouse/concepts/azure-delegated-resource-management.md) Gerencie eficientemente vários inquilinos, de uma única vista, sem ter de assinar no diretório de cada inquilino.

- Os prestadores de serviços podem gerir a postura de segurança dos recursos, para vários clientes, a partir de dentro do seu próprio inquilino.

- Equipas de segurança de organizações com vários inquilinos podem ver e gerir a sua postura de segurança a partir de um único local.

## <a name="set-up-cross-tenant-management"></a>Criar gestão de inquilinos cruzados

Instale a gestão de inquilinos cruzados, delegando acesso aos recursos de inquilinos geridos ao seu próprio inquilino utilizando a [gestão de recursos delegados do Azure.](../lighthouse/concepts/azure-delegated-resource-management.md)

> [!NOTE]
> A gestão de recursos delegados da Azure é um dos componentes-chave do Farol Azure.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Como funciona a gestão de inquilinos transintisos no Centro de Segurança

Você é capaz de rever e gerir subscrições em vários inquilinos da mesma forma que você gere várias subscrições em um único inquilino.

A partir da barra de menu superior, clique no ícone do filtro e selecione as subscrições, a partir do diretório de cada inquilino, você gostaria de ver.

  ![Filtrar inquilinos](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Os pontos de vista e as ações são basicamente os mesmos. Eis alguns exemplos:

- **Gerir políticas**de segurança : De uma forma, gerir a postura de segurança de muitos recursos com [políticas,](tutorial-security-policy.md)tomar ações com recomendações de segurança e recolher e gerir dados relacionados com a segurança.
- Melhorar a **pontuação segura e a postura**de conformidade : A visibilidade entre inquilinos permite-lhe visualizar a postura de segurança global de todos os seus inquilinos e onde e como melhor melhorar a [Pontuação Segura](security-center-secure-score.md) e a postura de [conformidade](security-center-compliance-dashboard.md) para cada um deles.
- **Recomendações de reparação**: Monitorize e remediar uma [recomendação](security-center-recommendations.md) para muitos recursos de vários inquilinos de uma só vez. Pode então enfrentar imediatamente as vulnerabilidades que apresentam o maior risco em todos os inquilinos.
- **Gerir Alertas**: Detetar [alertas](security-center-alerts-overview.md) em todos os diferentes inquilinos. Tome medidas sobre recursos que estão fora do cumprimento das medidas de [reparação](security-center-managing-and-responding-alerts.md)atodáveis.

- **Gerir funcionalidades avançadas**de defesa em nuvem e muito mais: Gerir os vários serviços de proteção contra ameaças, tais como [acesso vM just-in-time (JIT)](security-center-just-in-time.md), [Adaptive Network Hardening,](security-center-adaptive-network-hardening.md)controlos de [aplicações adaptativos,](security-center-adaptive-application.md)e muito mais.
 
## <a name="next-steps"></a>Passos seguintes
Este artigo explica como funciona a gestão de inquilinos no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Fortaleça a sua postura de segurança com](security-center-monitoring.md) o Azure Security Center - Aprenda a monitorizar a saúde dos seus recursos Azure.
* [Azure Security Center FAQ](faq-general.md) - Encontre perguntas frequentes sobre a utilização do serviço.
* [Conheça o Farol Azure em cenários empresariais](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) - Descubra como o Farol Azure pode simplificar a gestão de inquilinos transversais dentro de uma empresa que utiliza vários inquilinos da Azure AD.