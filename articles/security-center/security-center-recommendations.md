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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 7f6c0f2a311590219fb59bfe1ec63831c03e8af2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314441"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança no Centro de Segurança do Azure 
Este tópico explica como ver e compreender as recomendações no Azure Security Center para ajudá-lo a proteger os seus recursos Azure.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Este documento não é um guia passo a passo.
>

## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?

Recomendações são ações a tomar para garantir os seus recursos.

O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como remediar essas vulnerabilidades.

Cada recomendação fornece-lhe:

- Uma breve descrição do assunto.
- As medidas de reparação a tomar para implementar a recomendação.
- Os recursos afetados.

## <a name="monitor-recommendations"></a>Monitorizar recomendações <a name="monitor-recommendations"></a>

O Centro de Segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. O azulejo **de recomendações** no âmbito **do overview** mostra o número total de recomendações identificadas pelo Centro de Segurança.

![Visão geral do centro de segurança](./media/security-center-recommendations/asc-overview.png)

1. Selecione o **azulejo de recomendações** sob **visão geral**. A lista **de recomendações** abre.

1. As recomendações são agrupadas nos controlos de segurança.

      ![Recomendações agrupadas pelo controlo de segurança](./media/security-center-recommendations/view-recommendations.png)

1. Expanda um controlo e selecione uma recomendação específica para ver a página de recomendação.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Página de detalhes de recomendação." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    A página inclui:

    - **Impor** e **Negar** botões sobre recomendações apoiadas (ver [Prevenir configurações erradas com recomendações de Aplicação/Negação)](prevent-misconfigurations.md)
    - **Indicador de gravidade**
    - **Intervalo de frescura**  (se for caso disso) 
    - **Descrição** - Uma breve descrição da questão
    - **Medidas de reparação** - Descrição das etapas manuais necessárias para remediar a questão da segurança nos recursos afetados. Para recomendações com "correção rápida", pode selecionar **a lógica de remediação** do Ver antes de aplicar a correção sugerida aos seus recursos. 
    - **Recursos afetados** - Os seus recursos são agrupados em separadores:
        - **Recursos saudáveis** – Recursos relevantes que não são impactados ou sobre os quais já remediaram o problema.
        - **Recursos pouco saudáveis** – Recursos que ainda são impactados pela questão identificada.
        - **Recursos não aplicáveis** – Recursos para os quais a recomendação não pode dar uma resposta definitiva. O separador não aplicável também inclui razões para cada recurso. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Página de detalhes de recomendação.":::

## <a name="preview-recommendations"></a>Recomendações de pré-visualização

Recomendações assinaladas como **Preview** não estão incluídas nos cálculos da sua pontuação segura.

Devem continuar a ser remediados sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação.

Um exemplo de uma recomendação de pré-visualização:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Página de detalhes de recomendação.":::
 
## <a name="next-steps"></a>Passos seguintes

Neste documento, foi apresentado às recomendações de segurança no Centro de Segurança. Para aprender a remediar as recomendações:

- [Remediar recomendações](security-center-remediate-recommendations.md) — Aprenda a configurar políticas de segurança para as suas subscrições e grupos de recursos Azure.
- [Evitar configurações erradas com recomendações de Cumprir/Negar](prevent-misconfigurations.md).
