---
title: Recomendações de segurança no Centro de Segurança do Azure
description: Este documento acompanha-o através de como as recomendações no Azure Security Center ajudam a proteger os seus recursos Azure e a manter-se em conformidade com as políticas de segurança.
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
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79245307"
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
- As medidas de reparação a executar para implementar a recomendação. <!-- In some cases, Quick Fix remediation is available. -->
- Que recursos precisam que faça a ação recomendada neles.
- O **impacto Secure Score**, que é o valor que a sua Pontuação Segura irá subir se implementar esta recomendação.

## <a name="monitor-recommendations"></a>Monitorizar recomendações<a name="monitor-recommendations"></a>

O Centro de Segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. O azulejo **de recomendações** em termos de **visão geral** mostra o número total de recomendações identificadas pelo Centro de Segurança.

![Visão geral do centro de segurança](./media/security-center-recommendations/asc-overview.png)

1. Selecione o **azulejo de recomendações** em **visão geral**. A lista **de recomendações** abre.

      ![Ver recomendações](./media/security-center-recommendations/view-recommendations.png)

    Pode filtrar recomendações. Para filtrar as recomendações, selecione **Filter** na lâmina **de recomendações.** A lâmina **do Filtro** abre-se e seleciona a gravidade e os valores de estado que deseja ver.

   * **RECOMENDAÇÕES**: A recomendação.
   * **IMPACTO DE PONTUAÇÃO SEGURA**: Uma pontuação gerada pelo Security Center utilizando as suas recomendações de segurança e aplicando algoritmos avançados para determinar o quão crucial é cada recomendação. Para mais informações, consulte o [cálculo Secure Score](security-center-secure-score.md#secure-score-calculation).
   * **RECURSO**: Lista os recursos a que esta recomendação se aplica.
   * **BARRAS DE ESTATUTO**: Descreve a gravidade dessa recomendação específica:
       * **Alta (Vermelha)**: Existe uma vulnerabilidade com um recurso significativo (como uma aplicação, um VM ou um grupo de segurança de rede) e requer atenção.
       * **Médio (Laranja)**: Existe uma vulnerabilidade e são necessários passos não críticos ou adicionais para eliminá-lo ou concluir um processo.
       * **Baixa (Azul)**: Existe uma vulnerabilidade que deve ser abordada mas não requer atenção imediata. (Por predefinição, não são apresentadas recomendações baixas, mas pode filtrar recomendações baixas se quiser vê-las.) 
       * **Saudável (Verde)**:
       * **Não disponível (cinzento)**:

1. Para ver os detalhes de cada recomendação, clique na recomendação.

    ![Detalhes da recomendação](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Consulte [modelos clássicos e](../azure-classic-rm.md) de implantação de Recursos Manager para recursos Azure.
 
## <a name="next-steps"></a>Passos seguintes

Neste documento, foi apresentado às recomendações de segurança no Centro de Segurança. Para aprender a remediar as recomendações:

* [Remediar recomendações](security-center-remediate-recommendations.md) — Saiba como configurar as políticas de segurança para as suas subscrições e grupos de recursos Do Azure.
