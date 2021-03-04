---
title: Recomendações de segurança no Centro de Segurança do Azure
description: Este documento explica-lhe como as recomendações no Azure Security Center o ajudam a proteger os seus recursos Azure e a manter-se em conformidade com as políticas de segurança.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 57760443746e111750e74ef55fc18729f6ba32c4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100343"
---
# <a name="review-your-security-recommendations"></a>Reveja as suas recomendações de segurança

Este tópico explica como ver e compreender as recomendações no Azure Security Center para ajudá-lo a proteger os seus recursos Azure.

## <a name="monitor-recommendations"></a>Monitorizar recomendações <a name="monitor-recommendations"></a>

O Centro de Segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. 

1. A partir do menu do Centro de Segurança, abra a página **recomendações** para ver as recomendações aplicáveis ao seu ambiente. As recomendações são agrupadas nos controlos de segurança.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Recomendações agrupadas pelo controlo de segurança" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Para encontrar recomendações específicas do tipo de recurso, gravidade, ambiente ou outros critérios que sejam importantes para si, utilize os filtros opcionais acima da lista de recomendações.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtros para refinar a lista de recomendações do Centro de Segurança Azure":::

1. Expanda um controlo e selecione uma recomendação específica para ver a página de detalhes da recomendação.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Página de detalhes de recomendação." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    A página inclui:

    1. Para recomendações suportadas, a barra de ferramentas superior mostra qualquer ou todos os seguintes botões:
        - **Impor** e **Negar** (ver [Prevenir configurações erradas com recomendações de Aplicação/Negação)](prevent-misconfigurations.md)
        - **Ver definição de política** para ir diretamente para a entrada da Política Azure para a política subjacente
    1. **Indicador de gravidade**
    1. **Intervalo de frescura** (se for caso disso)
    1. **Contagem de recursos isentos** se existirem isenções para esta recomendação, isto mostra o número de recursos que foram isentos
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