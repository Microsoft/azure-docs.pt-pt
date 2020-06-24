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
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 4d65b43dad80cb130d582132d21e2d10bd8051dc
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791389"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança no Centro de Segurança do Azure 
Este tópico explica como ver e compreender as recomendações no Azure Security Center para ajudá-lo a proteger os seus recursos Azure.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Este documento não é um guia passo a passo.
>

## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?

Recomendações são ações a tomar para garantir os seus recursos.

O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como removê-las.

Cada recomendação fornece-lhe:

- Uma breve descrição do que está a ser recomendado.
- As medidas de reparação a tomar para implementar a recomendação. <!-- In some cases, Quick Fix remediation is available. -->
- Que recursos precisam de si para realizar a ação recomendada neles.
- O **impacto 'Pontuação Segura'** é a quantidade que o seu Secure Score irá subir se implementar esta recomendação.

## <a name="monitor-recommendations"></a>Monitorizar recomendações<a name="monitor-recommendations"></a>

O Centro de Segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. O azulejo **de recomendações** no âmbito **do overview** mostra o número total de recomendações identificadas pelo Centro de Segurança.

![Visão geral do centro de segurança](./media/security-center-recommendations/asc-overview.png)

1. Selecione o **azulejo de recomendações** sob **visão geral**. A lista **de recomendações** abre.

      ![Ver recomendações](./media/security-center-recommendations/view-recommendations.png)

    Pode filtrar recomendações. Para filtrar as recomendações, **selecione Filtro** na lâmina **recomendações.** A lâmina **do filtro** abre-se e seleciona a gravidade e os valores de estado que deseja ver.

   * **RECOMENDAÇÕES**: A recomendação.
   * **IMPACTO DE PONTUAÇÃO SEGURA**: Uma pontuação gerada pelo Security Center utilizando as suas recomendações de segurança e aplicando algoritmos avançados para determinar o quão crucial cada recomendação é. Para obter mais informações, consulte o [cálculo 'Pontuação Segura'.](secure-score-security-controls.md#how-your-secure-score-is-calculated)
   * **RECURSO**: Lista os recursos a que se aplica esta recomendação.
   * **BARRAS DE ESTATUTO**: Descreve a gravidade dessa recomendação específica:
       * **Alta (Vermelha)**: Existe uma vulnerabilidade com um recurso significativo (como uma aplicação, um VM ou um grupo de segurança de rede) e requer atenção.
       * **Médio (Laranja)**: Existe uma vulnerabilidade e são necessários passos não críticos ou adicionais para eliminá-lo ou concluir um processo.
       * **Baixa (Azul)**: Existe uma vulnerabilidade que deve ser abordada mas não requer atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas pode filtrar recomendações baixas se quiser vê-las.) 
       * **Saudável (Verde)**:
       * **Não disponível (Cinza)**:

1. Para ver os detalhes de cada recomendação, clique na recomendação.

    ![Detalhes da recomendação](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Consulte [os modelos clássicos e de implementação do Gestor de Recursos](../azure-classic-rm.md) para obter recursos da Azure.
 
## <a name="next-steps"></a>Passos seguintes

Neste documento, foi apresentado às recomendações de segurança no Centro de Segurança. Para aprender a remediar as recomendações:

* [Remediar recomendações](security-center-remediate-recommendations.md) — Aprenda a configurar políticas de segurança para as suas subscrições e grupos de recursos Azure.
