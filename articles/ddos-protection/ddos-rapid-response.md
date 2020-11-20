---
title: Azure DDos resposta rápida
description: Saiba como envolver especialistas em DDoS durante um ataque ativo para apoio especializado.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 3655b117b641a6179bd6539aaf3f75af07f21a1a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992374"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDos resposta rápida

Durante um acesso ativo, os clientes da Azure DDoS Protection Standard têm acesso à equipa DDoS Rapid Response (DRR), que pode ajudar na investigação de ataque durante um ataque, bem como na análise pós-ataque.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md).

## <a name="when-to-engage-drr"></a>Quando envolver a DRR

Só deve ativar a DRR se: 

- Durante um ataque DDoS se descobrir que o desempenho do recurso protegido está severamente degradado, ou o recurso não está disponível. Rever o passo 2 acima sobre a configuração de monitores para detetar problemas de disponibilidade e desempenho de recursos.
- Acha que o seu recurso está sob ataque DDoS, mas o serviço de proteção DDoS não está a atenuar o ataque de forma eficaz.
- Está a planear um evento viral que aumentará significativamente o tráfego da sua rede.
- Para ataques que têm um impacto crítico no negócio.

## <a name="engage-drr-during-an-active-attack"></a>Engate DRR durante um ataque ativo

1. A partir do portal Azure enquanto cria um novo pedido de suporte, escolha **o Tipo de Emissão** como Técnico.
2. Escolha **o Serviço** como **Proteção DDOS**.
3. Escolha um recurso no menu de entrega de recursos. _Tem de selecionar um Plano DDoS que esteja ligado à rede virtual protegida pela DDoS Protection Standard para ativar a DRR._

    ![Escolha o recurso](./media/ddos-rapid-response/choose-resource.png)

4. Na página seguinte **do Problema** selecione a **gravidade** como A -Critical Impact and **Problem Type** como 'Under attack'.

    ![PSeverity e Tipo de Problema](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Complete detalhes adicionais e envie o pedido de apoio.

A DRR segue o modelo de suporte Azure Rapid Response. Consulte [o âmbito de suporte e a capacidade de resposta](https://azure.microsoft.com/en-us/support/plans/response/) para obter mais informações sobre a Resposta Rápida..

Para saber mais, leia a [documentação Padrão de Proteção DDoS](./ddos-protection-overview.md).

## <a name="next-steps"></a>Próximos passos

- Saiba como [testar através de simulações.](test-through-simulations.md)
- Saiba [como visualizar e configurar a telemetria de proteção DDoS](telemetry-monitoring-alerting.md).
- Saiba como [configurar relatórios de mitigação de ataques dDoS e registos de fluxo](reports-and-flow-logs.md).