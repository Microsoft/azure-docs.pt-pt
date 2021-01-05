---
title: Recomendações de segurança no Centro de Segurança do Azure
description: Este documento explica-lhe como as recomendações no Azure Security Center o ajudam a proteger os seus recursos Azure e a manter-se em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/25/2020
ms.author: memildin
ms.openlocfilehash: 115d89783a849a9c4c7adb2fceceaf8d1575c785
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795993"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança no Centro de Segurança do Azure 
Este tópico explica como ver e compreender as recomendações no Azure Security Center para ajudá-lo a proteger os seus recursos Azure.


## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?

Recomendações são ações a tomar para garantir os seus recursos.

O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como remediar essas vulnerabilidades.

Cada recomendação fornece-lhe:

- Uma breve descrição da questão
- As medidas de reparação a realizar para implementar a recomendação
- Os recursos afetados

## <a name="monitor-recommendations"></a>Monitorizar recomendações <a name="monitor-recommendations"></a>

O Centro de Segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. 

1. A partir do menu do Centro de Segurança, abra a página **recomendações** para ver as recomendações aplicáveis ao seu ambiente. As recomendações são agrupadas nos controlos de segurança.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Recomendações agrupadas pelo controlo de segurança" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Para encontrar recomendações específicas do tipo de recurso, gravidade, ambiente ou outros critérios que sejam importantes para si, utilize os filtros opcionais acima da lista de recomendações.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtros para refinar a lista de recomendações do Centro de Segurança Azure":::

1. Expanda um controlo e selecione uma recomendação específica para ver a página de detalhes da recomendação.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Página de detalhes de recomendação." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    A página inclui:

    1. **Impor** e **Negar** botões sobre recomendações apoiadas (ver [Prevenir configurações erradas com recomendações de Aplicação/Negação)](prevent-misconfigurations.md)
    1. **Indicador de gravidade**
    1. **Intervalo de frescura**  (se for caso disso) 
    1. **Descrição** - Uma breve descrição da questão
    1. **Medidas de reparação** - Descrição das etapas manuais necessárias para remediar a questão da segurança nos recursos afetados. Para recomendações com "correção rápida", pode selecionar **a lógica de remediação** do Ver antes de aplicar a correção sugerida aos seus recursos. 
    1. **Recursos afetados** - Os seus recursos são agrupados em separadores:
        - **Recursos saudáveis** – Recursos relevantes que não são impactados ou sobre os quais já remediaram o problema.
        - **Recursos pouco saudáveis** – Recursos que ainda são impactados pela questão identificada.
        - **Recursos não aplicáveis** – Recursos para os quais a recomendação não pode dar uma resposta definitiva. O separador não aplicável também inclui razões para cada recurso. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Não recursos aplicáveis com razões.":::
    1. Botões de ação para remediar a recomendação ou desencadear uma aplicação lógica.

## <a name="preview-recommendations"></a>Recomendações de pré-visualização

Recomendações assinaladas como **Preview** não estão incluídas nos cálculos da sua pontuação segura.

Devem continuar a ser remediados sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação.

Um exemplo de uma recomendação de pré-visualização:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com a bandeira de pré-visualização":::
 
## <a name="next-steps"></a>Passos seguintes

Neste documento, foi apresentado às recomendações de segurança no Centro de Segurança. Para informações relacionadas:

- [Remediar recomendações](security-center-remediate-recommendations.md)--Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure.
- [Evitar configurações erradas com recomendações de Cumprir/Negar](prevent-misconfigurations.md).
- [Automatizar respostas ao Centro de Segurança dispara respostas](workflow-automation.md)--Automatizar respostas a recomendações
- [Isentar um recurso de uma recomendação](exempt-resource.md)
- [Recomendações de segurança: um guia de referência](recommendations-reference.md)