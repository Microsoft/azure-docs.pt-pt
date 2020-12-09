---
title: Gestão de inquilinos cruzados no Centro de Segurança Azure Microsoft Docs
description: Saiba como configurar a gestão de inquilinos cruzados para gerir a postura de segurança de vários inquilinos no Centro de Segurança usando a gestão de recursos delegada da Azure.
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
ms.openlocfilehash: d76ecc55c2e156199b2db789721946fd1f36de27
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922918"
---
# <a name="cross-tenant-management-in-security-center"></a>Gestão de inquilinos cruzados no Centro de Segurança

A gestão de inquilinos transversais permite-lhe visualizar e gerir a postura de segurança de vários inquilinos no Centro de Segurança, aproveitando a [gestão de recursos delegados da Azure.](../lighthouse/concepts/azure-delegated-resource-management.md) Gerir vários inquilinos de forma eficiente, de uma única vista, sem ter de se inscrever no diretório de cada inquilino.

- Os prestadores de serviços podem gerir a postura de segurança dos recursos, para vários clientes, a partir do seu próprio inquilino.

- Equipas de segurança de organizações com vários inquilinos podem ver e gerir a sua postura de segurança a partir de um único local.

## <a name="set-up-cross-tenant-management"></a>Configurar a gestão de múltiplos inquilinos

A gestão de recursos delegados da Azure é um dos componentes-chave do Farol Azure. Confiúdo da gestão de inquilinos, delegando o acesso aos recursos dos inquilinos geridos ao seu próprio inquilino utilizando estas instruções da documentação do Farol Azure: [Azure delegado gestão de recursos.](../lighthouse/concepts/azure-delegated-resource-management.md)


## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Como funciona a gestão de inquilinos cruzados no Centro de Segurança

Você é capaz de rever e gerir subscrições em vários inquilinos da mesma forma que você gere várias subscrições em um único inquilino.

A partir da barra de menu superior, clique no ícone do filtro e selecione as subscrições, a partir do diretório de cada inquilino, que você gostaria de ver.

  ![Inquilinos filtram](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

As opiniões e as ações são basicamente as mesmas. Eis alguns exemplos:

- **Gerir políticas** de segurança : De uma só vista, gerir a postura de segurança de muitos recursos com [políticas,](tutorial-security-policy.md)tomar ações com recomendações de segurança e recolher e gerir dados relacionados com a segurança.
- **Melhorar a pontuação segura e a postura** de conformidade : A visibilidade do inquilino transversal permite-lhe visualizar a postura de segurança geral de todos os seus inquilinos e onde e como melhor melhorar a postura de [pontuação](secure-score-security-controls.md) e [conformidade](security-center-compliance-dashboard.md) segura para cada um deles.
- **Remediar recomendações**: Monitorize e remedia uma [recomendação](security-center-recommendations.md) para muitos recursos de vários inquilinos de uma só vez. Em seguida, pode enfrentar imediatamente as vulnerabilidades que apresentam o maior risco em todos os inquilinos.
- **Gerir Alertas**: Detetar [alertas](security-center-alerts-overview.md) em todos os diferentes inquilinos. Tomar medidas sobre recursos que estejam fora do cumprimento das medidas de [reparação accitórias.](security-center-managing-and-responding-alerts.md)

- **Gerir funcionalidades avançadas** de defesa em nuvem e muito mais : Gerir os vários serviços de proteção de ameaças, tais como [acesso vm just-in-time (JIT),](security-center-just-in-time.md) [Hardening de Rede Adaptativa,](security-center-adaptive-network-hardening.md) [controlos de aplicações adaptativos](security-center-adaptive-application.md), e muito mais.
 
## <a name="next-steps"></a>Passos seguintes
Este artigo explica como funciona a gestão de inquilinos cruzados no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Fortaleça a sua postura de segurança com o Azure Security Center](security-center-monitoring.md) - Aprenda a monitorizar a saúde dos seus recursos Azure.
* [Azure Security Center FAQ](faq-general.md) - Encontre perguntas frequentes sobre a utilização do serviço.
* [Saiba mais sobre o Farol Azure em cenários empresariais](../lighthouse/concepts/enterprise.md) - Descubra como o Farol Azure pode simplificar a gestão de inquilinos cruzados dentro de uma empresa que utiliza vários inquilinos da AD Azure.