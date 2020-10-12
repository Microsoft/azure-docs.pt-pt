---
title: O painel principal do Azure Security Center ou a página 'overview'
description: Conheça as funcionalidades da página geral do Centro de Segurança
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3f7298ec7bba82eaddc4a002723f44bc5da68433
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945350"
---
# <a name="azure-security-centers-overview-page"></a>Página geral do Centro de Segurança Azure

Quando abre o Centro de Segurança Azure, a primeira página a aparecer é a página geral. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Página de descrição geral do Centro de Segurança":::

Descubra e avalie a segurança das suas cargas de trabalho e identifique e atenue os riscos, com a página geral do Centro de Segurança.

A visão geral proporciona uma visão unificada da postura de segurança das suas cargas de trabalho em nuvem híbrida. Além disso, mostra alertas de segurança, informações de cobertura, e muito mais.


## <a name="features-of-the-overview-page"></a>Características da página geral

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Página de descrição geral do Centro de Segurança":::

A **barra de menu superior** oferece:
- **Subscrições** - Pode visualizar e filtrar a lista de subscrições selecionando este botão. O Centro de Segurança ajustará o ecrã para refletir a postura de segurança das subscrições selecionadas.
- **Novidade** - Abre as [notas de lançamento](release-notes.md) para que possa manter-se atualizado com novas funcionalidades, correções de bugs e funcionalidades preprecadas.
- **Números de alto nível** para as contas de nuvem conectadas, para mostrar o contexto da informação nos azulejos principais abaixo. Bem como o número de recomendações e alertas ativos.
    Saiba mais sobre a ligação das suas [contas AWS](quickstart-onboard-aws.md) e dos seus [projetos GCP.](quickstart-onboard-gcp.md)


No centro da página encontram-se **quatro azulejos centrais,** cada um ligado a um dashboard dedicado para mais detalhes:
- **Pontuação segura** - O Security Center avalia continuamente os seus recursos, subscrições e organização para questões de segurança. Em seguida, agrega todas as conclusões numa única pontuação para que possa dizer, num ápice, a sua situação de segurança atual: quanto maior for a pontuação, menor o nível de risco identificado. [Saiba mais](secure-score-security-controls.md).
- **Compliance** - O Security Center fornece informações sobre a sua postura de conformidade com base em avaliações contínuas do seu ambiente Azure. O Security Center analisa fatores de risco no seu ambiente de nuvem híbrida de acordo com as melhores práticas de segurança. Estas avaliações são mapeadas para controlos de conformidade de um conjunto de normas apoiadas. [Saiba mais.](security-center-compliance-dashboard.md)
- **Azure Defender** - Esta é a plataforma de proteção da carga de trabalho em nuvem (CWPP) integrada no Centro de Segurança para cargas avançadas, inteligentes, de proteção das suas cargas de trabalho Azure e híbridas. O azulejo mostra a cobertura dos seus recursos conectados (para as subscrições atualmente selecionadas) e os alertas recentes, codificados por cores por severidade. [Saiba mais](azure-defender.md).
- **Inventário** - O azulejo mostra o número de VMs não monitorizados e um simples barómetro dos seus recursos monitorizados pelo Centro de Segurança. [Saiba mais](asset-inventory.md).


O **painel Insights** oferece itens personalizados para o seu ambiente, incluindo:
- Os seus recursos mais atacados
- Os seus controlos de segurança que têm o maior potencial para aumentar a sua pontuação segura
- As recomendações ativas com mais recursos impactados
- Publicações recentes de blogs por especialistas do Azure Security Center

## <a name="next-steps"></a>Próximos passos

Esta página introduziu a página geral do Centro de Segurança. Para obter informações relacionadas, consulte:

- [Explore e gere os seus recursos com ferramentas de inventário e gestão de ativos](asset-inventory.md)
- [Pontuação segura no Azure Security Center](secure-score-security-controls.md)