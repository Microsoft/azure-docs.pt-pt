---
title: Recomendações de segurança na central de segurança do Azure | Microsoft Docs
description: Este documento explica como as recomendações na central de segurança do Azure ajudam a proteger os recursos do Azure e a manter a conformidade com as políticas de segurança.
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
ms.openlocfilehash: 32b7f1d699c0d620d70614c441a8c18520c1b2d5
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201055"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança no Centro de Segurança do Azure 
Este tópico explica como exibir e entender as recomendações na central de segurança do Azure para ajudá-lo a proteger seus recursos do Azure.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Este documento não é um guia passo a passo.
>

## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?

As recomendações são ações a serem executadas para proteger seus recursos.

A central de segurança analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, ele fornece recomendações sobre como removê-los.

Cada recomendação fornece:

- Uma breve descrição do que está sendo recomendado.
- As etapas de correção para executar a fim de implementar a recomendação. <!-- In some cases, one-click remediation is available. -->
- Quais recursos são necessários para executar a ação recomendada neles.
- O **impacto de Pontuação segura**, que é o valor que sua pontuação de segurança aumentará se você implementar essa recomendação.

## Recomendações de monitor<a name="monitor-recommendations"></a>

A central de segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. O bloco **recomendações** em **visão geral** mostra o número total de recomendações identificadas pela central de segurança.

![Visão geral da central de segurança](./media/security-center-recommendations/asc-overview.png)

1. Selecione o **bloco recomendações** em **visão geral**. A lista de **recomendações** é aberta.

      ![Ver recomendações](./media/security-center-recommendations/view-recommendations.png)

    Você pode filtrar as recomendações. Para filtrar as recomendações, selecione **Filtrar** na folha **recomendações** . A folha **filtro** é aberta e você seleciona os valores de gravidade e estado que deseja ver.

   * **RECOMENDAÇÕES**: A recomendação.
   * **IMPACTO DE PONTUAÇÃO SEGURA**: Uma pontuação gerada pela central de segurança usando suas recomendações de segurança e aplicando algoritmos avançados para determinar a crucialidade de cada recomendação. Para obter mais informações, consulte [cálculo de Pontuação segura](security-center-secure-score.md#secure-score-calculation).
   * **RECURSO**: Lista os recursos aos quais essa recomendação se aplica.
   * **BARRAS DE STATUS**:  Descreve a severidade dessa recomendação específica:
       * **Alta (vermelho)** : Existe uma vulnerabilidade com um recurso significativo (como um aplicativo, uma VM ou um grupo de segurança de rede) e requer atenção.
       * **Médio (laranja)** : Existe uma vulnerabilidade e etapas não críticas ou adicionais são necessárias para eliminá-la ou para concluir um processo.
       * **Baixa (azul)** : Existe uma vulnerabilidade que deve ser resolvida, mas não requer atenção imediata. (Por padrão, as recomendações baixas não são apresentadas, mas você pode filtrar com recomendações baixas se quiser vê-las.) 
       * **Íntegro (verde)** :
       * **Não disponível (cinza)** :

1. Para exibir os detalhes de cada recomendação, clique na recomendação.

    ![Detalhes da recomendação](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Consulte [modelos de implantação clássicos e do Resource Manager](../azure-classic-rm.md) para recursos do Azure.
 
## <a name="next-steps"></a>Passos seguintes

Neste documento, você foi apresentado às recomendações de segurança na central de segurança. Para saber como corrigir as recomendações:

* [Corrigir recomendações](security-center-remediate-recommendations.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
